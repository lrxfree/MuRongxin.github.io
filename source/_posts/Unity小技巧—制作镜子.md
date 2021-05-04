---
title: Unity小技巧—制作镜子
date: 2021-05-03 10:04:06
tags: 
    - unity
    - unity技巧
cover: ../image/镜子.png
highlight_shrink: true
---

> 镜子的实现效果如封面所示，还是非常不错的；不要添加额外的摄像机。
>
> 需要shader编写，为镜子的shader改为这个，并挂载相应的C#脚本——

- #### **C#脚本**

  ```c#
  using UnityEngine;
  using System.Collections;
  
  
  [ExecuteInEditMode] // Make mirror live-update even when not in play mode
  public class MirrorReflection : MonoBehaviour
  {
      public bool m_DisablePixelLights = true;
      public int m_TextureSize = 256;
      public float m_ClipPlaneOffset = 0.07f;
  
      public LayerMask m_ReflectLayers = -1;
  
      private Hashtable m_ReflectionCameras = new Hashtable(); // Camera -> Camera table
  
      private RenderTexture m_ReflectionTexture = null;
      private int m_OldReflectionTextureSize = 0;
  
      private static bool s_InsideRendering = false;
  
      // This is called when it's known that the object will be rendered by some
      // camera. We render reflections and do other updates here.
      // Because the script executes in edit mode, reflections for the scene view
      // camera will just work!
      public void OnWillRenderObject()
      {
          var rend = GetComponent<Renderer>();
          if (!enabled || !rend || !rend.sharedMaterial || !rend.enabled)
              return;
  
          Camera cam = Camera.current;
          if (!cam)
              return;
  
          // Safeguard from recursive reflections.        
          if (s_InsideRendering)
              return;
          s_InsideRendering = true;
  
          Camera reflectionCamera;
          CreateMirrorObjects(cam, out reflectionCamera);
  
          // find out the reflection plane: position and normal in world space
          Vector3 pos = transform.position;
          Vector3 normal = transform.up;
  
          // Optionally disable pixel lights for reflection
          int oldPixelLightCount = QualitySettings.pixelLightCount;
          if (m_DisablePixelLights)
              QualitySettings.pixelLightCount = 0;
  
          UpdateCameraModes(cam, reflectionCamera);
  
          // Render reflection
          // Reflect camera around reflection plane
          float d = -Vector3.Dot(normal, pos) - m_ClipPlaneOffset;
          Vector4 reflectionPlane = new Vector4(normal.x, normal.y, normal.z, d);
  
          Matrix4x4 reflection = Matrix4x4.zero;
          CalculateReflectionMatrix(ref reflection, reflectionPlane);
          Vector3 oldpos = cam.transform.position;
          Vector3 newpos = reflection.MultiplyPoint(oldpos);
          reflectionCamera.worldToCameraMatrix = cam.worldToCameraMatrix * reflection;
  
          // Setup oblique projection matrix so that near plane is our reflection
          // plane. This way we clip everything below/above it for free.
          Vector4 clipPlane = CameraSpacePlane(reflectionCamera, pos, normal, 1.0f);
          //Matrix4x4 projection = cam.projectionMatrix;
          Matrix4x4 projection = cam.CalculateObliqueMatrix(clipPlane);
          reflectionCamera.projectionMatrix = projection;
  
          reflectionCamera.cullingMask = ~(1 << 4) & m_ReflectLayers.value; // never render water layer
          reflectionCamera.targetTexture = m_ReflectionTexture;
          GL.SetRevertBackfacing(true);
          reflectionCamera.transform.position = newpos;
          Vector3 euler = cam.transform.eulerAngles;
          reflectionCamera.transform.eulerAngles = new Vector3(0, euler.y, euler.z);
          reflectionCamera.Render();
          reflectionCamera.transform.position = oldpos;
          GL.SetRevertBackfacing(false);
          Material[] materials = rend.sharedMaterials;
          foreach (Material mat in materials)
          {
              if (mat.HasProperty("_ReflectionTex"))
                  mat.SetTexture("_ReflectionTex", m_ReflectionTexture);
          }
  
          // Restore pixel light count
          if (m_DisablePixelLights)
              QualitySettings.pixelLightCount = oldPixelLightCount;
  
          s_InsideRendering = false;
      }
  
  
      // Cleanup all the objects we possibly have created
      void OnDisable()
      {
          if (m_ReflectionTexture)
          {
              DestroyImmediate(m_ReflectionTexture);
              m_ReflectionTexture = null;
          }
          foreach (DictionaryEntry kvp in m_ReflectionCameras)
              DestroyImmediate(((Camera)kvp.Value).gameObject);
          m_ReflectionCameras.Clear();
      }
  
  
      private void UpdateCameraModes(Camera src, Camera dest)
      {
          if (dest == null)
              return;
          // set camera to clear the same way as current camera
          dest.clearFlags = src.clearFlags;
          dest.backgroundColor = src.backgroundColor;
          if (src.clearFlags == CameraClearFlags.Skybox)
          {
              Skybox sky = src.GetComponent(typeof(Skybox)) as Skybox;
              Skybox mysky = dest.GetComponent(typeof(Skybox)) as Skybox;
              if (!sky || !sky.material)
              {
                  mysky.enabled = false;
              }
              else
              {
                  mysky.enabled = true;
                  mysky.material = sky.material;
              }
          }
          // update other values to match current camera.
          // even if we are supplying custom camera&projection matrices,
          // some of values are used elsewhere (e.g. skybox uses far plane)
          dest.farClipPlane = src.farClipPlane;
          dest.nearClipPlane = src.nearClipPlane;
          dest.orthographic = src.orthographic;
          dest.fieldOfView = src.fieldOfView;
          dest.aspect = src.aspect;
          dest.orthographicSize = src.orthographicSize;
      }
  
      // On-demand create any objects we need
      private void CreateMirrorObjects(Camera currentCamera, out Camera reflectionCamera)
      {
          reflectionCamera = null;
  
          // Reflection render texture
          if (!m_ReflectionTexture || m_OldReflectionTextureSize != m_TextureSize)
          {
              if (m_ReflectionTexture)
                  DestroyImmediate(m_ReflectionTexture);
              m_ReflectionTexture = new RenderTexture(m_TextureSize, m_TextureSize, 16);
              m_ReflectionTexture.name = "__MirrorReflection" + GetInstanceID();
              m_ReflectionTexture.isPowerOfTwo = true;
              m_ReflectionTexture.hideFlags = HideFlags.DontSave;
              m_OldReflectionTextureSize = m_TextureSize;
          }
  
          // Camera for reflection
          reflectionCamera = m_ReflectionCameras[currentCamera] as Camera;
          if (!reflectionCamera) // catch both not-in-dictionary and in-dictionary-but-deleted-GO
          {
              GameObject go = new GameObject("Mirror Refl Camera id" + GetInstanceID() + " for " + currentCamera.GetInstanceID(), typeof(Camera), typeof(Skybox));
              reflectionCamera = go.GetComponent<Camera>();
              reflectionCamera.enabled = false;
              reflectionCamera.transform.position = transform.position;
              reflectionCamera.transform.rotation = transform.rotation;
              reflectionCamera.gameObject.AddComponent<FlareLayer>();
              go.hideFlags = HideFlags.HideAndDontSave;
              m_ReflectionCameras[currentCamera] = reflectionCamera;
          }
      }
  
      // Extended sign: returns -1, 0 or 1 based on sign of a
      private static float sgn(float a)
      {
          if (a > 0.0f) return 1.0f;
          if (a < 0.0f) return -1.0f;
          return 0.0f;
      }
  
      // Given position/normal of the plane, calculates plane in camera space.
      private Vector4 CameraSpacePlane(Camera cam, Vector3 pos, Vector3 normal, float sideSign)
      {
          Vector3 offsetPos = pos + normal * m_ClipPlaneOffset;
          Matrix4x4 m = cam.worldToCameraMatrix;
          Vector3 cpos = m.MultiplyPoint(offsetPos);
          Vector3 cnormal = m.MultiplyVector(normal).normalized * sideSign;
          return new Vector4(cnormal.x, cnormal.y, cnormal.z, -Vector3.Dot(cpos, cnormal));
      }
  
      // Calculates reflection matrix around the given plane
      private static void CalculateReflectionMatrix(ref Matrix4x4 reflectionMat, Vector4 plane)
      {
          reflectionMat.m00 = (1F - 2F * plane[0] * plane[0]);
          reflectionMat.m01 = (-2F * plane[0] * plane[1]);
          reflectionMat.m02 = (-2F * plane[0] * plane[2]);
          reflectionMat.m03 = (-2F * plane[3] * plane[0]);
  
          reflectionMat.m10 = (-2F * plane[1] * plane[0]);
          reflectionMat.m11 = (1F - 2F * plane[1] * plane[1]);
          reflectionMat.m12 = (-2F * plane[1] * plane[2]);
          reflectionMat.m13 = (-2F * plane[3] * plane[1]);
  
          reflectionMat.m20 = (-2F * plane[2] * plane[0]);
          reflectionMat.m21 = (-2F * plane[2] * plane[1]);
          reflectionMat.m22 = (1F - 2F * plane[2] * plane[2]);
          reflectionMat.m23 = (-2F * plane[3] * plane[2]);
  
          reflectionMat.m30 = 0F;
          reflectionMat.m31 = 0F;
          reflectionMat.m32 = 0F;
          reflectionMat.m33 = 1F;
      }
  }
  ```

  - #### **shader**
  
  ```C#
  Shader "FX/MirrorReflection"
  {
  	Properties
  	{
  		_MainTex ("Base (RGB)", 2D) = "white" {}
  		[HideInInspector] _ReflectionTex ("", 2D) = "white" {}
  	}
  	SubShader
  	{
  		Tags { "RenderType"="Opaque" }
  		LOD 100
   
  		Pass {
  			CGPROGRAM
  			#pragma vertex vert
  			#pragma fragment frag
  			#include "UnityCG.cginc"
  			struct v2f
  			{
  				float2 uv : TEXCOORD0;
  				float4 refl : TEXCOORD1;
  				float4 pos : SV_POSITION;
  			};
  			float4 _MainTex_ST;
  			v2f vert(float4 pos : POSITION, float2 uv : TEXCOORD0)
  			{
  				v2f o;
  				o.pos = UnityObjectToClipPos (pos);
  				o.uv = TRANSFORM_TEX(uv, _MainTex);
  				o.refl = ComputeScreenPos (o.pos);
  				return o;
  			}
  			sampler2D _MainTex;
  			sampler2D _ReflectionTex;
  			fixed4 frag(v2f i) : SV_Target
  			{
  				fixed4 tex = tex2D(_MainTex, i.uv);
  				fixed4 refl = tex2Dproj(_ReflectionTex, UNITY_PROJ_COORD(i.refl));
  				return tex * refl;
  			}
  			ENDCG
  	    }
  	}
  }
  ```
  
  

