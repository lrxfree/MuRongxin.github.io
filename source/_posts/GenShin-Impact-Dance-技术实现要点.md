---
title: GenShin Impact Dance - 技术实现要点
date: 2021-03-03 07:58:24
tags: unity
cover: ../image/封面15_2.jpg
---

#### 一、挂载控制组件

> 通过拖拽，挂载GameController.cs，在该脚本里面挂载其它组件。
>
> ```c#
> private Inputhandler m_Inputhandler;
> private UIManager m_UIManager;
> private GameModelHandler m_GameModelHandler;
> 
> privete Awake()
> {
>     m_UIManager = gameObject.AddComponent<UIManager>();
>     m_Inputhandler = gameObject.AddComponent<Inputhandler>();
> }
> ```
>
> 这样都不用 .GetComponent<>(); 

**关于单例模式，再次强调：**

- 切换场景不能销毁；
- 场景切回不能创建多个；

```c#
public static GameController Instance { get; set; }
private void Awake()
{
   if (GameController.Instance != null)
    {
      Destroy(gameObject);
      return;
    }
    Instance = this;
    DontDestroyOnLoad(gameObject);

}
```

#### 二、模型导入问题

- 模型导入unity后，有的模型会出现材质球纹理丢失问题，而有的模型由不会，目前原因我还没有弄清楚。如果出问题了，需要手动指定 shader 为Mmd4Mecanim类下面的，并设置相应的Texture。

