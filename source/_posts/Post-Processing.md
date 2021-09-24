---
title: Post Processing
date: 2021-06-11 08:06:08
tags: unity
cover: https://z3.ax1x.com/2021/09/11/hxinw8.jpg
---

# Post Processing

> 后处理工具，能十分明显的提高画面效果；

<table>
    <tr>
        <td>Before</td>
        <td>After</td>
    </tr>
    <tr>
        <td><img src="https://z3.ax1x.com/2021/09/10/hjItFe.png" alt="hjItFe.png" border="0" /></td>
        <td><img src="https://z3.ax1x.com/2021/09/10/hjIzm6.png" alt="hjIzm6.png" border="0" /></td>
    </tr>
</table>


#### 一、安装使用

将这部分单独拿出来说，是因为在URP/HDRP工程与一般工程里使用有点不一样；

> 在无通用渲染管线的工程里面使用

- 在 Package manager里面安装Post Processing 的包；
- 在工程里面创建`Post-Processing Profile`文件(配置文件)；![](https://z3.ax1x.com/2021/09/10/hj7iQA.png)
- 创建空物体，适当重命名，添加 `Post Process Volume` 组件，并将配置文件赋值给这个组件；修改这个物体的Layer，可以新建一个专门的渲染层；
- 勾选isGlobal，全局响应；
- 选中主摄像机，添加 `Post Process Layer` 组件，并在组件中选择渲染层；

> 在URP/HDRP工程使用

- 创建 `Global Volume`，在其组件中创建配置文件；
- 勾选主摄像机中的 `Use Post Process` ;
- 在URP资源中勾选HDR；

#### 二、具体效果

> Ambient Occasion环境光遮蔽（AO）；
>
> 增加阴影细节，可以特别观察转角处的阴影效果；

[![hvtLef.png](https://z3.ax1x.com/2021/09/10/hvtLef.png)](https://imgtu.com/i/hvtLef)

![](https://z3.ax1x.com/2021/09/10/hvUh2d.png)

- Mode：默认即可；
- Intensity：阴影效果；
- Thickness Modifier：阴影的渐隐范围；
- Color：阴影颜色；
- Ambient Only：仅仅影响环境光照，用于延时渲染和HDR

> Bloom辉光效果
>
> 从明亮区域发散出光；

![](https://z3.ax1x.com/2021/09/10/hvwu4O.png)

![](https://z3.ax1x.com/2021/09/10/hvww8g.png)

- Intensity：强度；
- Threshold：阈值，可以进一步控制强度；
- Clamp：光的量；
- Dirtness：
- - Dirt Texture：能将光线按照贴图透过，
  - Dirt Intensity：强度；

> Chromatic Aberration 色差
>
> 模拟镜头颜色偏移；

<img src="https://z3.ax1x.com/2021/09/10/hvrYQg.png" style="zoom:80%;" />




> Vignette 边角压暗/暗角效果
>
> 为了突出画面中间的部分或是营造一种压迫的氛围；


![](https://z3.ax1x.com/2021/09/11/hxQQbQ.png)
![](https://z3.ax1x.com/2021/09/11/hx1z3q.png)

- Color：暗角的颜色；
- Center：非暗角区域的位置；
- Intensity：强度/范围；
- Smoothness：过渡范围；
- Rounded：非暗角区域是否为圆形；


> Depth of Field 景深（DOF）
>
> 聚焦物体，虚化背景；


![](https://z3.ax1x.com/2021/09/11/hx80Qx.png)

- Mode：拥有两种模式；
- 其它属性不做解释，在工程里面实际实验一下就能明白；

> Motion Blur 运动模糊

![](https://z3.ax1x.com/2021/09/11/hxJHRs.png)
![](https://z3.ax1x.com/2021/09/11/hxtNB6.png)

- 属性不多做解释，在工程里面实际实验一下就能明白；

>  Lens Distortion 镜头扭曲
>
>  使画面扭曲

![](https://z3.ax1x.com/2021/09/11/hxNNxs.png)
![](https://z3.ax1x.com/2021/09/11/hxN8IS.png)

- Scale：屏幕的缩放；

> Panini Projection 帕尼尼投影
>
> 放大画面中部，保持四周的大小

![](https://z3.ax1x.com/2021/09/11/hxUALq.png)

![](https://z3.ax1x.com/2021/09/11/hxU6Tf.png)

----

>  Dithering 抖色
>
>  >消除色阶

![](https://z3.ax1x.com/2021/09/11/hxasgJ.png)
![](https://z3.ax1x.com/2021/09/11/hxdpvj.png)

> Anti-Aliasing 反锯齿、抗锯齿、边缘柔化

![](https://z3.ax1x.com/2021/09/11/hxdXLR.png)
![](https://z3.ax1x.com/2021/09/11/hx0Sns.png)

-----
> Tonemapper 色调映射
> 使颜色更加分明,颜色风格化；	

![](https://z3.ax1x.com/2021/09/11/hxseG6.png)
![](https://z3.ax1x.com/2021/09/11/hxsyiq.png)

- Mode：具有三种模式，
- - None：不采用；
- - Neutral：比较折中的方案；
- - ACES：使画面更加电影化；

> White Balance 白平衡
>
> 暖色调/冷色调

![](https://z3.ax1x.com/2021/09/11/hx2gr8.png)
![](https://z3.ax1x.com/2021/09/11/hxRmRI.png)

- Temperature:调整冷暖色调；
- Tint：调整绿色和紫色；

> Color Adjustment 色彩调整

![](https://z3.ax1x.com/2021/09/11/hxWbEF.png)
![](https://z3.ax1x.com/2021/09/11/hxfiUe.png)

- Post Exposure：曝光度；
- Contrast：对比度；
- Color Filter：颜色过滤；
- Hue Shift：色相偏移；
- Saturation：饱和度；

> Color Curves 颜色曲线；

![](https://z3.ax1x.com/2021/09/11/hx4Dj1.png)

![](https://z3.ax1x.com/2021/09/11/hx44gA.png)

- Hue vs Hue：颜色替换,将画面中的某种颜色替换为另一种颜色；
  ![](https://z3.ax1x.com/2021/09/11/hxjgEj.png)
- Hue vs Sat：调整特定颜色的饱和度；
- 还是要在工程里面实验呀；

> 色调分离

![](https://z3.ax1x.com/2021/09/11/hxxchn.png)

> 通道混合器
>
> 调整 R G B 的量；

![](https://z3.ax1x.com/2021/09/11/hxz3vV.png)

> Shadows Midtones Highlights 阴影中间调高光 

![](https://z3.ax1x.com/2021/09/11/hz9sK0.png)

> Lift Gamma Gain

![](https://z3.ax1x.com/2021/09/11/hzPa1s.png)
![](https://z3.ax1x.com/2021/09/11/hzPvut.png)


> Film Grain 胶片颗粒
>
> 能营造一种陈旧年代感；

![](https://z3.ax1x.com/2021/09/11/hzkIH0.png)
![](https://z3.ax1x.com/2021/09/11/hzF7SH.png)


>Color Lookup 颜色查找 
>
>LUT 调色

![](https://z3.ax1x.com/2021/09/11/hzia5D.png)

#### 三、代码控制

通过代码控制后处理的各种效果；
主要思路：修改 profile 文件；

```C#
using unityEngine.render.postprocessing;

private Post
```

