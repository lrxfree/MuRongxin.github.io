---
title: Unity 2D 灯光的使用
date: 2021-01-05 16:53:14
tags: 
      - unity
      - unity渲染
category: unity
cover: ../image/2dlight2.PNG
---

## unity2D光照的使用

在unity里面，我们可以使用2D光源让场景变得更加好看。

[![RoYQ76.png](https://z3.ax1x.com/2021/07/06/RoYQ76.png)

如上图所示，这是一个没有加入光源的2D夜晚场景，看起来虽然有夜晚的意思，但是很单调。

> 我们在使用2D光源之前，需要先通过 **Package Manager**安装 *Universal RP*，这样才能开始使用2d光源。

[![RotVVP.png](https://z3.ax1x.com/2021/07/06/RotVVP.png)

> 在使用光源之前，需要创建光照渲染器。

[![](https://z3.ax1x.com/2021/07/06/Rotya6.png)

> 将自动创建的 pipeline_Renderer 删除，创建一个2D Renderer。

[![RoN8QH.png](https://z3.ax1x.com/2021/07/06/RoN8QH.png)

> 将2d Renderer指定给2d pipeline的Renderer List；

[![RoUMBn.png](https://z3.ax1x.com/2021/07/06/RoUMBn.png)

> 再设置一下pipeline

[![RoURud.png](https://z3.ax1x.com/2021/07/06/RoURud.png)

> 更换材质，换为能被2D光源影响的材质，

[![RoUHgg.png](https://z3.ax1x.com/2021/07/06/RoUHgg.png)

> 然后，场景可能会变黑，因为场景里面没有光源；场景里的物体就能受到2D光源的影响；
>
> 现在，我们就能愉快的使用2D光源了。

[![Roau8O.png](https://z3.ax1x.com/2021/07/06/Roau8O.png)

