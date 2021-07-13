---
title: UI上如何显示旋转模型
date: 2021-01-04 15:26:50
tags: unity
cover: ../image/人物面板2.jpeg
category: unity
---

在UI上操作模型

> 在开发中，我们需要在某个UI界面上展示模型，比如背包、人物面板。

<table>
    <tr>
        <td><center>
            <img src="https://z3.ax1x.com/2021/07/06/RodHmj.jpg" width="60%"></center>
        </td>
        <td><center>
            <img src="https://z3.ax1x.com/2021/07/06/RowPB9.jpg"></center>
        </td>
    </tr>
</table>



>  我们可以使用 RenderTexture，实现这样的效果；

- 创建RawImage，将RenderTexture赋值给*Texture*属性，该RawImage就会显示RenderTexture上的内容。

- 创建新的Camera，用于渲染需要显示到UI界面的物体，将创建好的RenderTexture赋值给Camera的*Target Texture*属性；

- 新Camera所照射到的内容就会显示到UI上，

> 展示模型就如同上面所描述的那样实现；要旋转、放大物体，其实就是操作Camera所照射的那个物体；

- 将以下代码添加到游戏物体上；

```c#
public class RotateModel {
    public Transform modelTransform;//需要操作的模型；
    private bool isRotate;//是否
    private Vector3 startPoint
    private Vector3 startAngle;
    private float rotateScale=1f;
    
    private void Start() {
        
    }

    private void Update() {
        if(Input.getMouseButtonDown(0)&&!isRotate){
            isRotate=true;
            startPoint=Input.mousePostion;
            startAngle=modelTransform.eulerAngles;
        }
        if(Input.getMouseButtonUp(0)){
            isRotate=false;
        }
        if(isRotate)
        {
            var currentPoint=Input.mousePostion;
            var x=startPoint.x-currentPoint.x;

            modelTransform.eulerAngles=startAngle+new Vector3(0,x*rotateScale,0);
        }
    }
}
```

> 将上面的代码添加到游戏物体上之后，游戏物体就会随着鼠标的移动而旋转；但是，没有范围的旋转限制，我们需要完善这个问题。
>
> 在需要交互的区域上面铺盖一层image，将透明度调高，添加 Event Trigger组件，添加相应的事件。!

![](https://z3.ax1x.com/2021/07/06/RowMBd.png)



> 并需要对脚本增加方法，去掉原本脚本里面重复的部分，在组件里面指定特定的方法。

```c#
 public void BeginDrag(){
        isRotate=true;
        startPoint=Input.mousePostion;
        startAngle=modelTransform.eulerAngles;
    }

    public void Drag(){
        if(isRotate)
        {
            var currentPoint=Input.mousePostion;
            var x=startPoint.x-currentPoint.x;

            modelTransform.eulerAngles=startAngle+new Vector3(0,x*rotateScale,0);
        }
    }

    public void EndDrag(){
        isRotate=false;
    }
    
```

