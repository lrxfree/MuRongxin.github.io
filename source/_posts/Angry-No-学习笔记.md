---
title: Angry No 学习笔记
date: 2019-07-03 13:51:03
tags: unity
cover: ../image/unity_Log.png
---

**Angry Note Solution**

#### 一、鼠标事件

> 游戏物体跟随鼠标的移动，非UI物体的点击事件; 当用户在 碰撞体 或 GUIElement 上按鼠标按钮时调用；

```c#
private void OnMouseDown(){
    transform.position = Camera.main.ScreenToWorldPoint(Input.mousePosition);//将鼠标的位置给目标位置；
    transform.position += new Vector3(0, 0, 10);//修正目标物体的 Z 轴坐标；
}
```



```c#
//限制物体随鼠标移动的范围（圆圈）：
 public Transform limitPosition;//在Inspector面板要赋值一个限制点；
 public float limitDistance=1f;

Vector3.Distance(transform.position ,limitPosition.position)>limitDistance //Vector3 中的 Distance 方法 计算 两个向量之间的位移；
        
Vector3 tempPostion = (transform.position - limitPosition.position).normalized;//单位化向量，获得向量的方向；
tempPostion = tempPostion * limitDistance;//为设置好的向量长度，添加方向；
transform.position = limitPosition.position + tempPostion;

2、鼠标水平拖动控制：需要限制 Y 轴的移动；
```

#### 二、使用 LineRenderer 组件 划线：

```c#
public LineRenderer liftLine; //划线组件
public LineRenderer rightLine; //划线组件
public Transform liftPosition; //端点的位置；
public Transform rightPosition;//端点的位置；
                               // 各位置 和组件 需要在Inspect面板种赋值；
                     		   //一条线的某个端点要添加LineRenderer组件；注意层级关系；
private void DrawLine() //划线的方法
 {
    liftLine.SetPosition(0, liftPosition.position);//一个点；
    liftLine.SetPosition(1, transform.position);//另一个点；

    rightLine.SetPosition(0, transform.position);
    rightLine.SetPosition(1, limitPosition.position);
 }
```

#### 三、UI控制

1. UI的渲染，默认高于所有的游戏物体，如果要改变渲染层级，则需要新建新的摄像机；更改 Canvas 的 Render Moder；

2. 相机之间的渲染层级由深度值（Depth）决定；

3. 实例

   ```c#
   public void ShowStars()  //显示星星；
   {
      StartCoroutine("Display");//开启协程
   }
   
   IEnumerator Display()  //使用协程，使星星依次显示；
   {
      yield return new WaitForSeconds(0.3f);
      for (int i = 0; i < birds.Count + 1; i++)   
           starts[i].SetActive(true);       
   }
   ```

   

4. Hierarchy面板下，在 Canvas ，UI有层级关系；越往下，层级越大，大的UI层会挡住小的UI层，鼠标无法穿过大的UI层点击小的UI层；

#### 四、相机跟随

> 需要**相机**在游戏物体移出范围之后**跟随移动**。

- 设置一个相机跟随的平滑度

  ```c#
  public float smoothMove = 2.5f;
  ```

- 记录目标物体的 X轴 坐标

  ```c#
  float tempPosX = transform.position.x;
  ```

- 动态的设置相机的 X轴坐标：

  ```c#
  Camera.main.transform.position = Vector3.Lerp(Camera.main.transform.position, new Vector3(Mathf.Clamp(tempPosX, 0, 15), Camera.main.transform.position.y, Camera.main.transform.position.z), smoothMove * Time.deltaTime);
  ```

- 使用差值，使相机实现平滑的运动：

  ```c#
  Camera.main.transform.position = Vector3.Lerp（Vector3 a，Vector3 b，float t）；
  -1、Vector3 a：当前点；
  -2、Vector3 b：目标点；
  -3、float t：移动的速度；
  ```

- 限制跟随的范围：

  ``` c#
  Mathf.Clamp（float value，float min，float max）；-1、float value：要被限制的数字；-2、float min：范围最小值；-3、float max：范围最大值；-4、当 float value 小于 float min 时，返回 float min 的值；    当 float value 大于 float max 时，返回 float max 的值； 当 float value 的值在 float min 和 float max 之间时，返回 float value 实时的值；
  ```

  

- 注意，float t/*平滑度，移动的速度*/，要乘以 Time.deltaTime，这样才能正常的移动；

```c#
2、限制跟随的范围：Mathf.Clamp（float value，float min，float max）；-1、float value：要被限制的数字；-2、float min：范围最小值；-3、float max：范围最大值；-4、当 float value 小于 float min 时，返回 float min 的值；    当 float value 大于 float max 时，返回 float max 的值； 当 float value 的值在 float min 和 float max 之间时，返回 float value 实时的值； 3、注意，float t/*平滑度，移动的速度*/，要乘以 Time.deltaTime，这样才能正常的移动；
```

#### 六、注意虚方法的使用

#### 七、Grid Layout Group 组件

   用于UI的网格布局，在父物体上挂载 Grid Layout Group 组件
   设置 单个物体的大小，各物体之间的间隔，在父物体下不断复制时，就可以自动的排开；父物体的  	大小决定排列的范围；

#### 八、检测是否点击到 UI 界面

```c#
using UnityEngine.EventSystems;EventSystem.current.IsPointerOverGameObject()//判断是否点击了UI界面；                                             //返回 true / false
```

#### 九、游戏数据存档

1. PlayerPrefs 游戏存档：
   在游戏会话中储存和访问游戏存档。这个是持久化数据储存，比如保存游戏记录。

2. Static Functions 静态函数
   -1、DeleteAll   
   从游戏存档中删除所有key和数据，请谨慎使用。

   -2、PlayerPrefs.DeleteKey 删除键和它对应的值；
    PlayerPrefs.DeleteKey("KeyName");

   -3、PlayerPrefs.HasKey 使用有键
    如果key在游戏存档中存在，返回true。
    PlayerPrefs.HasKey("KillName");

   -4、PlayerPrefs.Save 保存
    写入所有修改参数到硬盘。
    默认Unity在程序退出时保存参数。在游戏崩溃或过早退出时，想要写入游戏存档到游戏中合理的“检查点”；
    这个函数将写入数据到硬盘，游戏可能有稍微的停顿，因此它不建议在实际游戏期间调用。
   -5、
   		```
   	PlayerPrefs.GetFloat 获得浮点数
     	PlayerPrefs.GetInt 获得整数
     	PlayerPrefs.GetString 获得字符串
      	```

       3. 如果存在，返回游戏存档文件中key对应的数值；如果不存在，将返回默认值。（默认值可以不设置）。```c# 
          Value = PlayerPrefs.GetFloat("Health", 0);
          Value = PlayerPrefs.GetInt("Score", 0);
          Value = PlayerPrefs.GetString("Name", "No Name");```

   4.

   ```c#
    PlayerPrefs.SetFloat 设置浮点数； 设置由key确定的浮点数值 PlayerPrefs.SetFloat("Health", 50.0F); PlayerPrefs.SetInt 设置整数；public static void SetInt(string key, int value);设置由key键确定的整数值。PlayerPrefs.SetInt("Score", 20);PlayerPrefs.SetString 设置字符串；public static void SetString(string key, string value); 设置由key确定的字符串值。PlayerPrefs.SetString("Name", _PlayerName);
   ```

#### 十、关卡选择

```c#
private bool isCanSelect;//判断该关卡是否可选 
public Sprite levelBackground;//关卡可选择时的图片 
private Image image;//UI更换图片的组件；    
public GameObject[] showStarsArrow;//关卡图标下面要显示的星星；   
void Start () { //在界面开始的时候就执行操作；              
    if (transform.parent.GetChild(0).name == gameObject.name)//判断是否是第一关；       
    {                                                        
        //脚本挂载在关卡图标上；           
        isCanSelect = true;       
    } else//如果该关卡不是第一关；       
    {        ；
        //数据存储的键都为 关卡名；          
        int lastLevel = int.Parse(gameObject.name) - 1;
     //取得前一关的名字 名字要数字命名，方便查找与加载；           
     if (PlayerPrefs.GetInt(lastLevel.ToString())> 0)//判断前一关的星星数；           
     {               
         isCanSelect = true;           
     }     
    }       
    if (isCanSelect)//判断关卡可选之后的操作；      
    {          
        image.overrideSprite = levelBackground;//UI更换图片的方法和其它物体不太一样； 
        transform.Find("Num").gameObject.SetActive(true);//找到子物体里面，name为“Num”的游戏物体，    
    }      
        int showStarNum = PlayerPrefs.GetInt(gameObject.name);//获得当前关卡是几颗星星；     
        for(int i = 0; i < showStarNum;i++)//显示星星；       
        {           
            showStarsArrow[i].SetActive(true);   
        }        
    }
    public void Select() //为关卡图标上挂载的 Button 组件提供鼠标注册事件；  
    {      
        if (isCanSelect)  
        {                                                     
            //根据设置，在点下关卡图标时存储；         
            PlayerPrefs.SetString("nowLevel", gameObject.name);
            //存储这是哪个关卡，键 的名字为："nowLevel"，存储的值为：关卡的名字：
            gameObject.name；                                                             
                //我觉得    这是之后整个数据存储的基础；      
                SceneManager.LoadScene(3);//跳转到游戏场景；    
        }  
    }
```

#### 十一、实例化游戏场景

1、将整个游戏场景作为一个预制体，多个场景预制体之间的差别就是关卡之间的差别。
2、代码实现：

```c#
 private void Awake()//该脚本挂载在 游戏场景的相机上；  
 {        
     Instantiate(Resources.Load(PlayerPrefs.GetString("nowLevel")));//实例化关卡；        //下面的代码没有实际的作用，       
     Resources.Load(PlayerPrefs.GetString("nowLevel"));//从Resources文件夹里面加载资源，        
     Resources.Load("");//从Resources文件夹里面记载资源，要传进去要加载的资源的名称；这里为："nowLevel"这个键对应的值（关卡名字）；   
 }                     
//Resources文件夹名不能拼写出错；
```

#### 十二、得分数据的存储

```c#
//SaveData()方法可以在点击 return retry  这些按钮时调用；
public void SaveData()   
{       
    if(starsNum> PlayerPrefs.GetInt(PlayerPrefs.GetString("nowLevel")))//校验，当现在的星星数大于之前的星星数，才进行数据存储；    
    {           
        PlayerPrefs.SetInt(PlayerPrefs.GetString("nowLevel"), starsNum);//存储当前关卡的星星数量；只要能够得到关卡的名字就能知道该关卡获得了多少星星；                                         
        //获取 键 的名字：
        PlayerPrefs.GetString("nowLevel")；"nowLevel"里面存储着当前关卡的名字；    
    }     
    int LevelNum=6；//关卡数；       
    int sum = 0;// 统计整个地图星星的总数；   
    for(int i = 0; i < LevelNum; i++)//统计整个地图星星的总数；   
    {          
        sum += PlayerPrefs.GetInt(i.ToString());   
    }  
    //PlayerPrefs.GetInt(i.ToString()) 获取每一关的星星数；
    PlayerPrefs.SetInt("totalStars", sum);//存储星星总数；
}   
```

#### 十三、地图的选择

```c#
public int starsNum;//总共获得了多少星星，用于控制地图是否可选，数值 在 Inspect 面板里根据不同的地图而设置；
private bool isSelect;//控制该地图是否可选
public GameObject locks;//锁的图标；
public GameObject stars;//该地图的星星数显示；
public Text starsText;// Use this for initialization
void Start ()
{      
    if(EventSystem.current.IsPointerOverGameObject())//判断是否点击了UI界面；需要设置UI不与屏幕交互；       
        return；//如果点击的是UI界面，就不进行下面的操作；   
        if (PlayerPrefs.GetInt("totalStars", 0) >= starsNum)//PlayerPrefs.GetInt("totalStars", 0);获取 "totalStars" 里面存储的数据，如果没有存储，就返回 0；      
        {         
            locks.SetActive(false);       
            stars.SetActive(true);        
            isSelect = true;          // PlayerPrefs.GetInt("totalStars", 0);// 获取 "totalStars" 里面存储的数据，如果没有存储，就返回 0；本句代码在这里不起实际作用；  
        }      
    starsText.text = PlayerPrefs.GetInt("totalStars", 0).ToString()+"/10";       //PlayerPrefs.DeleteAll();//清除所有储存的数据；  
}// Update is called once per frame
void Update () {  
}  
public void LoadLeveScene()//进入关卡界面；鼠标注册事件；  
{      
    if (isSelect)   
    {        
        SceneManager.LoadScene(2); 
    }   
}
```

