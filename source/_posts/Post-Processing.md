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

> 