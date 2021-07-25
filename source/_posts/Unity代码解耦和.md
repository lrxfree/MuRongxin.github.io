---
title: Unity代码解耦和
date: 2021-05-15 13:24:48
tags: unity
cover: ../image/事件广播.jpg
category: unity
---

### Unity代码解耦和

在一个类里面，对另一个类进行操作，比如设置字段的值，调用方法等；最直接想到的方式是采用单例模式，或是在允许的情况下将字段设置成static，通过`类名.字段名`的方式来实现；我个人觉得，如果大量采用这样的方式，代码的耦合度会不断变高，如果只创建一个采用了单例模式的类，比如`GameController`,在这个类里面，持有对其它必要类的引用，通过这些引用使用具体类里面的方法，emmm，怎么说呢，这样的方式在引用变多之后，也会觉得有点麻烦，具体的方法在具体的类里面定义，在`GameController`里面还要在写一次方法；

我在这里记录一下自己学到的另一个降低代码耦合度的方法，比较适合代码量比较大的情况——事件广播与监听系统。

> 先给出使用实例

```c#
public class AudioSourceManager : MonoBehaviour
{   

    public void Awake()
    {
        //事件注册，添加监听；
        EventCenter.AddListener<string, bool>(EventType.PlayMusic, PlayMusic);
    }
    
     private void OnDestroy()
    {
        //当销毁的时候移除事件监听；
        EventCenter.RemoveLister<string,bool>(EventType.PlayMusic, PlayMusic)
    }
    
    //需要在合适的时机调用的具体方法；
    private void PlayMusic()
    {
        //.....
    }
}


public class Character : MonoBehaviour
{
    //.....
    public void Dance()
    {
        //假设，这里需要PlayMusic；
        //广播事件码，启动相应的方法；
        EventCenter.Broadcast<string, bool>(EventType.PlayMusic, "DanceMusic", false);
    }
}

```

以上展示的，就是这整个事件系统的使用流程，注册事件监听=>广播事件码，这样就能实现在一个类里面，调用另一个类里面的方法；

> 下面，我们开始解析这个事件广播与监听系统

> > `EventDelegate`类，它封装了这个系统会使用到的委托：

```c#
/*定义委托*/
public delegate void EventDelegate();
public delegate void EventDelegate<T>(T arg);
public delegate void EventDelegate<T, X>(T arg1, X arg2);
public delegate void EventDelegate<T, X, Y>(T arg1, X arg2, Y arg3);
```

可以根据具体的系统需求增加对应参数的委托；

> > `EventType`类，里面是一个枚举类型，定义需要用到的事件码；

```c#
public enum EventType
{
    PlaySound,
    PlayMusic,
    StopMusic,
    卫宫士郎,
    GameOver,
    PlayWinAnime,
    PlayLostAnime,
}		
```

> > `EventCenter`类，整个系统的核心，在代码注释里面详细解释；其实代码很容以理解，实现的基本思路就是通过关键字EventType在字典Dictionary<EventType, Delegate>里面找到存放的委托，然后执行这个委托方法；添加监听就是往字典里面添加委托数据，移除监听就是删除字典里面的相关数据；

```c#
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class EventCenter
{
    private static Dictionary<EventType, Delegate> m_EvenTable = new Dictionary<EventType, Delegate>();//创建字典，存放事件码和委托；

    //
    private static void OnListennerAdding(EventType eventType, Delegate eventDelegate)
     
        if (!m_EvenTable.ContainsKey(eventType))//事件码是否已经存在；
        {
            m_EvenTable.Add(eventType, null);//委托先为空；
        }

        Delegate d = m_EvenTable[eventType];
        if (d != null && d.GetType() != eventDelegate.GetType())
        {
            throw new Exception(string.Format("尝试为事件{0}添加不同类型的委托，当前事件对应的委托是{1},要添加的委托类型为{2}", eventType, d.GetType(), eventDelegate.GetType()));
        }
    }

    private static void OnListennerRemoving(EventType eventType, Delegate eventDelegate)
    {
        if (m_EvenTable.ContainsKey(eventType))
        {
            Delegate @delegate = m_EvenTable[eventType];

            if (@delegate == null)
            {
                throw new Exception(string.Format("移除监听错误：事件{0}没有对应的委托", eventType));
            }
            else if (@delegate.GetType() != eventDelegate.GetType())
            {
                throw new Exception(string.Format("移除监听错误：尝试为事件{0}移除不同类型的监听，当前委托类型为{1}，要移除的委托类型为{2}", eventType, @delegate, eventDelegate));
            }
        }
        else
        {
            throw new Exception(string.Format("移除监听错误：没有事件码{0}", eventType));
        }
    }

    private static void OnListennerRemoved(EventType eventType)
    {
        if (m_EvenTable[eventType] == null)
        {
            m_EvenTable.Remove(eventType);
        }
    }
    //无参数的添加监听的方法；
    public static void AddListener(EventType eventType, EventDelegate eventDelegate)
    {
        OnListennerAdding(eventType, eventDelegate);//检查添加的监听
        m_EvenTable[eventType] = (EventDelegate)m_EvenTable[eventType] + eventDelegate;
    }

    //带一个参数的添加监听的方法；
    public static void AddListener<T>(EventType eventType, EventDelegate<T> eventDelegate)
    {
        OnListennerAdding(eventType, eventDelegate);
        m_EvenTable[eventType] = (EventDelegate<T>)m_EvenTable[eventType] + eventDelegate;
    }
    //带两个个参数的添加监听的方法；
    public static void AddListener<T, X>(EventType eventType, EventDelegate<T, X> eventDelegate)
    {
        OnListennerAdding(eventType, eventDelegate);
        m_EvenTable[eventType] = (EventDelegate<T, X>)m_EvenTable[eventType] + eventDelegate;
    }
    public static void AddListener<T, X, Y>(EventType eventType, EventDelegate<T, X, Y> eventDelegate)
    {
        OnListennerAdding(eventType, eventDelegate);
        m_EvenTable[eventType] = (EventDelegate<T, X, Y>)m_EvenTable[eventType] + eventDelegate;
    }
    //移除监听的方法;
    public static void RemoveLister(EventType eventType, EventDelegate eventDelegate)
    {
        OnListennerRemoving(eventType, eventDelegate);
        m_EvenTable[eventType] = (EventDelegate)m_EvenTable[eventType] - eventDelegate;
        OnListennerRemoved(eventType);
    }

    //
    public static void RemoveLister<T>(EventType eventType, EventDelegate<T> eventDelegate)
    {
        OnListennerRemoving(eventType, eventDelegate);
        m_EvenTable[eventType] = (EventDelegate<T>)m_EvenTable[eventType] - eventDelegate;
        OnListennerRemoved(eventType);
    }
    //
    public static void RemoveLister<T, X>(EventType eventType, EventDelegate<T, X> eventDelegate)
    {
        OnListennerRemoving(eventType, eventDelegate);
        m_EvenTable[eventType] = (EventDelegate<T, X>)m_EvenTable[eventType] - eventDelegate;
        OnListennerRemoved(eventType);
    }
    //
    public static void RemoveLister<T, X, Y>(EventType eventType, EventDelegate<T, X, Y> eventDelegate)
    {
        OnListennerRemoving(eventType, eventDelegate);
        m_EvenTable[eventType] = (EventDelegate<T, X, Y>)m_EvenTable[eventType] - eventDelegate;
        OnListennerRemoved(eventType);
    }

    //广播监听
    public static void Broadcast(EventType eventType)
    {
        Delegate d;

        if (m_EvenTable.TryGetValue(eventType, out d))
        {
            EventDelegate eventDelegate = d as EventDelegate;

            if (eventDelegate != null)
            {
                eventDelegate();
            }
            else
            {
                throw new Exception(string.Format("广播事件错误，事件{0}对应委托具有不同的类型", eventType));
            }
        }
    }

    //
    public static void Broadcast<T>(EventType eventType, T arg)
    {
        Delegate d;

        if (m_EvenTable.TryGetValue(eventType, out d))
        {
            EventDelegate<T> eventDelegate = d as EventDelegate<T>;

            if (eventDelegate != null)
            {
                eventDelegate(arg);
            }
            else
            {
                throw new Exception(string.Format("广播事件错误，事件{0}对应委托具有不同的类型", eventType));
            }
        }
    }
    //
    public static void Broadcast<T, X>(EventType eventType, T arg, X arg2)
    {
        Delegate d;

        if (m_EvenTable.TryGetValue(eventType, out d))
        {
            EventDelegate<T, X> eventDelegate = d as EventDelegate<T, X>;

            if (eventDelegate != null)
            {
                eventDelegate(arg, arg2);
            }
            else
            {
                throw new Exception(string.Format("广播事件错误，事件{0}对应委托具有不同的类型", eventType));
            }
        }
    }
    //
    public static void Broadcast<T, X, Y>(EventType eventType, T arg, X arg2, Y arg3)
    {
        Delegate d;

        if (m_EvenTable.TryGetValue(eventType, out d))
        {
            EventDelegate<T, X, Y> eventDelegate = d as EventDelegate<T, X, Y>;

            if (eventDelegate != null)
            {
                eventDelegate(arg, arg2, arg3);
            }
            else
            {
                throw new Exception(string.Format("广播事件错误，事件{0}对应委托具有不同的类型", eventType));
            }
        }
    }

}

```

> > 这个系统真的很好用，我已经用过好几次了！
>
> 还是要再说一下它的优点，以及它的缺点；
>
> > 解决了代码之间高度的耦合性，各个脚本只需要关注于自己本身的功能实现，不需要和外面联系，它不需要知道自己方法的广播者是谁，只需要监听事件就可以；广播者也只需要负责广播就好，不需要关心到底有没有脚本在监听，结果如何都不会报错；
> 
> > 添加监听的时候，参数的类型、顺序要和使用的泛型一致，广播监听的时候，参数的类型、顺序也要和添加监听时一致；这两点需要特别注意，在事件变多的时候，可能会造成不必要的bug，并且很可能不会产生明显的报错，会给解决带来不必要的麻烦。

