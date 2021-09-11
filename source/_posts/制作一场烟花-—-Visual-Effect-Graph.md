---
title: 制作一场烟花 — Visual Effect Graph
date: 2021-06-09 08:19:03
tags: unity
cover: https://z3.ax1x.com/2021/09/10/hjlPG6.png
---

#### 使用 Visual Effect Graph 制作好看的烟花

##### 一、前置准备

Visual Effect Graph 现在可以在 Unity HDRP 和 URP工程中使用；

使用之前。需要在 Package manager 里面安装Visual Effect Graph包和Post Progressing包，Post Progressing用来美化烟花效果；

> 新建visual effect graph文件：

![](https://z3.ax1x.com/2021/09/10/hXSXyF.png)

> 为了使用GPU事件，需要勾选Experimental Operators/Blocks；使用GPU事件，是为了用粒子触发粒子，比如粒子消亡时触发新的粒子特效；

![](https://z3.ax1x.com/2021/09/10/hXpaYq.png)



##### 二、添加烟花；

> 设置数量、Y方向随机速度、随机生命周期；

<img src="https://z3.ax1x.com/2021/09/10/hX9ja9.png" style="zoom:80%;" />

* 将Y方向的速度设置在一个范围内随机，烟花就会以不同的初始速度飞起来；

> 添加重力；现在粒子不受重力作用，不真实且粒子还会飞出屏幕外；

![](https://z3.ax1x.com/2021/09/10/hXFDeS.png)

* 在Update 节点添加 Gravity 块；

> 改变粒子生成的位置；现在所有的粒子都是从同一个位置出现，需要修改；

![](https://z3.ax1x.com/2021/09/10/hXk8XV.png)

* 添加Position(line)块，将X设置一个合适的范围，这样粒子就能在一个X范围内生成；

> 更改粒子外观

![](https://z3.ax1x.com/2021/09/10/hXlS7n.png)

* 在Output节点里面修改Blend Mode(混合模式)为`Additive`；修改材质为默认Particle材质；固定大小；没有设置颜色之前粒子还不会发光，需要拉高`intensity`的数值;

<img src="https://z3.ax1x.com/2021/09/10/hX3nW6.png" style="zoom:87%;" />

##### 三、烟花炸开的部分

> 添加GPU事件

![](https://z3.ax1x.com/2021/09/10/hX8rjO.png)

* 在Update节点添加触发事件—`Trigger Event On Die`,在该粒子消亡之后触发新的粒子；从Evt拖出新的粒子节点，我们需要在GPUEvent后面继续完善爆炸之后的特效，创建新的粒子系统；

![](https://z3.ax1x.com/2021/09/10/hXJRpt.png)

> 设置爆开烟花

![](https://z3.ax1x.com/2021/09/10/hXYaNj.png)

* 添加该设置之后，粒子就会出现在一角；

![](https://z3.ax1x.com/2021/09/10/hXYjPA.png)

* 需要添加`inherit source position`，使新的粒子继承消逝粒子的位置；

![](https://z3.ax1x.com/2021/09/10/hXNeFH.png)

![](C:\Users\PC\AppData\Roaming\Typora\typora-user-images\image-20210910111956335.png)

* 可以提高数量，以加强效果；

![](https://z3.ax1x.com/2021/09/10/hXU2VS.png)

* 目前所有的粒子都是停留在同一个位置，需要在Initialize节点添加随机速度，使粒子爆开；

![](https://z3.ax1x.com/2021/09/10/hXagQ1.png)

![](https://z3.ax1x.com/2021/09/10/hXaXef.png)

* 现在的粒子可以往任一方向飞行，没有减速，不真实，我们需要空气阻力，在Update节点添加线性阻力(linear drag)，调整数值；

![](https://z3.ax1x.com/2021/09/10/hXdGTO.png)

* 添加一定的重力，使之能够缓慢下落；

![](https://z3.ax1x.com/2021/09/10/hXw8vn.png)

> 在Output节点修改粒子爆开之后的效果，

* 使用随机大小；

<img src="https://z3.ax1x.com/2021/09/10/hX0hwV.png" style="zoom:80%;" />

* 在爆开的过程中改变颜色；

![](https://z3.ax1x.com/2021/09/10/hXBAmt.png)

​	 修改渐变

![](https://z3.ax1x.com/2021/09/10/hXDd58.png)

​		[闪光的间距越大，闪的越明显，原理就是在初始颜色的前面添加一个白色的色块]

![](https://z3.ax1x.com/2021/09/10/hXDRaV.png)

![](https://z3.ax1x.com/2021/09/10/hXD7rR.png)

> 烟花轨迹

用触发爆炸的思路来触发烟火轨迹，只需要改变触发类型；对于轨迹触发，我们需要选择 `Trigger Event Always`(一直触发）

![](https://z3.ax1x.com/2021/09/10/hjAlFg.png)

* 从evt拖出新的粒子节点，继续创建新的粒子系统，

![](https://z3.ax1x.com/2021/09/10/hjEGND.png)

* 设置粒子数量与生命周期，并使这些粒子继承源位置，

![](https://z3.ax1x.com/2021/09/10/hjVeVf.png)

![](https://z3.ax1x.com/2021/09/10/hjVtaT.png)

​	现在的轨迹看起来是分散的，效果很差；

* 使轨迹扩散，添加随机速度；

![](https://z3.ax1x.com/2021/09/10/hjeVN8.png)

![](https://z3.ax1x.com/2021/09/10/hjeQun.png)

* 将粒子沿Y方向拉长，就能使轨迹看起来像一个整体；颜色使用渐变而不是固定；

* 在Output节点设置大小和颜色；

  ![](https://z3.ax1x.com/2021/09/10/hjntX9.png)

> 爆炸的痕迹

爆炸痕迹和烟花轨迹是同样的思路，所以，我们可以复制轨迹的粒子系统给爆炸的粒子；

* 在爆炸部分粒子系统的Update节点添加触发事件—`Trigger Event Rate`(按速率生成),在该粒子消亡之后触发新的粒子；从Evt拖出新的粒子节点

![](https://z3.ax1x.com/2021/09/10/hjKLF0.png)

* 直接将轨迹的粒子系统复制过来，修改容量、去掉Set scale、调整生命周期、调整颜色；

![](https://z3.ax1x.com/2021/09/10/hjQtC6.png)

> 结果

![](https://z3.ax1x.com/2021/09/10/hjlPG6.png)

![](https://z3.ax1x.com/2021/09/10/hj8BSP.gif)

> 稍微调整参数，便能做出很厉害的效果；

![](https://z3.ax1x.com/2021/09/10/hjcqW6.gif)