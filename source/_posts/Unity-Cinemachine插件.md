---
title: Unity Cinemachine插件
date: 2020-08-11 21:31:10
tags: unity插件
cover: https://z3.ax1x.com/2021/08/12/fdbC6g.jpg
category: unity
---

## Virtual Cameras

Unity虚拟摄像机系统，非常的强大，能通过简单的方式调出很厉害的镜头运动。虚拟相机的效果最好在工程里实际试一下，很难在文档里面描述；

虚拟摄像机能为我们：

* 在场景中放置摄像机；
* 让Unity摄像机在运动中对准某物体，持续拍摄；
* 添加程序噪声到Unity摄像机。噪音可以模拟手持效果等；

![](https://docs.unity3d.com/Packages/com.unity.cinemachine@2.8/manual/images/CinemachineVCamProperties.png)

### 主要属性

|          属性          |                                                              |
| :--------------------: | ------------------------------------------------------------ |
|        **Solo**        |                                                              |
| **Game Window Guides** | 在Game视图显示可视化的缓冲区域；                             |
|      **Priority**      | 在多个虚拟摄像机中，该值越大，优先级越高；但是，当使用 虚拟摄像机在Timeline里被使用时，该值无效； |
|**Follow**|指定虚拟摄像机跟随的对象，Body属性使用这个目标来更新Unity摄像机的位置；|
|**Look At**|指定虚拟摄像机瞄准的对象，Aim属性使用这个目标来更新Unity摄像机的旋转。保持此属性为空；|
|**Standby Update**|控制虚拟相机不活动时虚拟相机更新的频率；|
|**Lens**|这个条目，太琐碎，需在项目里面直接看效果；|
|*Field Of View*||
|*Presets*|预设|
|。。。。||
|**Blend Hint**|提供有关混合进出虚拟相机的位置的提示。|
|**Inherit Position** |当启用这个虚拟摄像机时，如果可能的话，强制初始位置与Unity摄像机的当前位置相同。|

### Body 属性

![](https://docs.unity3d.com/Packages/com.unity.cinemachine@2.8/manual/images/CinemachineBody.png)

使用Body属性来指定场景中移动虚拟摄像机的算法。这部分不详细说明，需要

- [**Do Nothing**](https://docs.unity3d.com/Packages/com.unity.cinemachine@2.8/manual/CinemachineBodyDoNothing.html): does not move the Virtual Camera.
- [**3rd Person follow**](https://docs.unity3d.com/Packages/com.unity.cinemachine@2.8/manual/Cinemachine3rdPersonFollow.html): Pivots the camera horizontally and vertically around the player, to the **Follow** target.
- [**Framing Transposer**](https://docs.unity3d.com/Packages/com.unity.cinemachine@2.8/manual/CinemachineBodyFramingTransposer.html): moves in a fixed screen-space relationship to the **Follow** target.
- [**Hard Lock to Target**](https://docs.unity3d.com/Packages/com.unity.cinemachine@2.8/manual/CinemachineBodyHardLockTarget.html): uses the same position at the **Follow** target.
- [**Orbital Transposer**](https://docs.unity3d.com/Packages/com.unity.cinemachine@2.8/manual/CinemachineBodyOrbitalTransposer.html): moves in a variable relationship to the **Follow** target, optionally accepting player input.
- [**Tracked Dolly**](https://docs.unity3d.com/Packages/com.unity.cinemachine@2.8/manual/CinemachineBodyTrackedDolly.html): moves along a predefined path.
- [**Transposer**](https://docs.unity3d.com/Packages/com.unity.cinemachine@2.8/manual/CinemachineBodyTransposer.html): moves in a fixed relationship to the **Follow** target.

#### Aim 属性

使用Aim属性来指定如何旋转虚拟摄像机。

- [**Do Nothing**](https://docs.unity3d.com/Packages/com.unity.cinemachine@2.8/manual/CinemachineAimDoNothing.html): Do not procedurally rotate the Virtual Camera.
- [**Composer**](https://docs.unity3d.com/Packages/com.unity.cinemachine@2.8/manual/CinemachineAimComposer.html): Keep the **Look At** target in the camera frame.
- [**Group Composer**](https://docs.unity3d.com/Packages/com.unity.cinemachine@2.8/manual/CinemachineAimGroupComposer.html): Keep multiple **Look At** targets in the camera frame.
- [**Hard Look At**](https://docs.unity3d.com/Packages/com.unity.cinemachine@2.8/manual/CinemachineAimHardLook.html): Keep the **Look At** target in the center of the camera frame.
- [**POV**](https://docs.unity3d.com/Packages/com.unity.cinemachine@2.8/manual/CinemachineAimPOV.html): Rotate the Virtual Camera based on the user’s input.
- [**Same As Follow Target**](https://docs.unity3d.com/Packages/com.unity.cinemachine@2.8/manual/CinemachineAimSameAsFollow.html): Set the camera’s rotation to the rotation of the **Follow** target.

#### Noise 属性

在虚拟相机中使用Noise属性来模拟相机抖动。

### 轨道摄像机

![](https://docs.unity3d.com/Packages/com.unity.cinemachine@2.8/manual/images/CinemachinePathScene.png)

![](https://docs.unity3d.com/Packages/com.unity.cinemachine@2.8/manual/images/CinemachinePathInspector.png)

