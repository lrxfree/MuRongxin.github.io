---
title: TankAttack 学习笔记
date: 2019-04-13 14:19:07
tags: unity
category: unity
cover: ../image/tanktake.jpg
---

## TankAttack 学习笔记

### 一、监听水平方向和垂直方向输入的内容：

```c#
float h = Input.GetAxisRaw("Horizontal");//水平方向的输入；
 transform.Translate(Vector2.right * h * moveSpeed*Time.deltaTime,Space.World);//移动方法

 float v = Input.GetAxisRaw("Vertical");  //垂直方向的输入；
 transform.Translate(Vector2.up * v * moveSpeed * Time.deltaTime, Space.World);//移动方法
 
```

###  二、用代码更换图片；

```c#
private SpriteRenderer sp; //定义；
public Sprite[] tankSprites;//储存待更换的图片
 
 //获取SpriteRenderer组件，用于改变图片；
 void Start () {
        sprite = GetComponent<SpriteRenderer>();
	}
 
 //满足一定条件之后，更换图片；
 if (v == 1){
            sp.sprite = tankSprites[0];
        }
		
```

### 三、触发/碰撞检测：

> <1>发生碰撞的两个物体身上，都要有<碰撞器>,其中一个要有<刚体>(这一个最好是运动的那个物体，<刚体>如果长时间不运动会休眠)
>
> <2>触发器(Trigger)：不会受到力的作用。碰撞器(Collision)：

> <3>触发检测：

```c#
private void OnTriggerEnter2D(Collider2D collision)//一个碰撞器进入触发器；调用此方法。
{
        
}
```

### 四、角度旋转问题：

```c#
private Vector3 bulletEulAngle; //定义目标物体的旋转角度，<欧拉角>为<Vector3>类型；
public GameObject bulletPrefab; //定义需要实例化出来的目标物体的预制；

 bulletEulAngle = new Vector3(0, 0, 90);//角度赋值；
 Instantiate(bulletPrefab, transform.position, Quaternion.Euler(transform.eulerAngles + bulletEulAngle));
//转化角度的方法/  当前的角度 + 应改要旋转的角度；
```

> 五、XXXXXX.SetActive(false);只能设置自己身上的组件。如果A物体成了B物体的父物体，那么B物体也可以说是A物体身上的组件。

### 六、使用 布尔值 作为开关来辨识敌人与自己的子弹、产生不同类型的坦克；

```c#
public bool isBulletType; //子弹的类型，true为自己的子弹，false为敌人的子弹，默认false；
                           //在Inspector面板中，is Bullet Type 打上勾，就为true；
 switch (collision.tag)
        {
            case "Tank"://敌人的子弹打中自己的话；
                if (!isBulletType)//本来就是敌人的子弹，为false，如果 不再否定一次，把它变为true，就不会满足条件。
                {
                    collision.SendMessage("TankDie");
                }
               
                break;
 		}	
```

```c#
public bool bornType; //用于控制生成物体的类型，true为自己，false为敌人；默认false；
public GameObject[] enemyPrafabList; //多种类型的敌人；
  
 if (bornType)
    {
       Instantiate(tankPrefab, transform.position, Quaternion.identity);
    }
 else
    {
       int rad = Random.Range(0, 2);
       Instantiate(enemyPrafabList[rad], transform.position, Quaternion.identity);
    }
```

### 七、敌人自动移动、自动发射武器：

```c#
 public float bulletInterval = 1f;//子弹攻击间隔时间；
 public float rotationLimit = 3;//坦克旋转时间限制；
 
 private Vector3 bulletEulAngle;//子弹随坦克旋转而旋转的角度
 public GameObject bulletPrefab;//子弹预制体
 
 private int v, h;/*敌人旋转的方向，h：水平 ，v：垂直；定为全局变量的原因，如果是局部变量的话，每帧调用 TankMove() 方法时，h 和 v 的值都会被重置，所以，坦克就无法正常前进，
              只能转向；（TankMove()方法里面，使用局部变量要先赋值，才能使用）*/

 void TankMove()
    {
        
        if (rotationLimit <= 0)
        {
            int rotationRad = Random.Range(0, 13);

            if (rotationRad < 3)
            {
                v = 1;
                h = 0;
            }
            else if (rotationRad >= 3 && rotationRad < 7)
            {
                v = -1;
                h = 0;
            }
            else if (7 <= rotationRad && rotationRad < 10)
            {
                h = 1;
                v = 0;
            }
            else if (rotationRad >= 10 && rotationRad < 12)
            {
                h = -1;
                v = 0;
            }

            rotationLimit = 3;

        }
        
		else
        {
            rotationLimit -= Time.deltaTime;
        }

        
        transform.Translate(Vector2.right * h * moveSpeed * Time.deltaTime, Space.World);
        if (h == -1)
        {
            sprite.sprite = tankSprites[3];
            bulletEulAngle = new Vector3(0, 0, 90);
        }
        if (h == 1)
        {
            sprite.sprite = tankSprites[1];
            bulletEulAngle = new Vector3(0, 0, -90);
        }

        //垂直方向的输入；
        transform.Translate(Vector2.up * v * moveSpeed * Time.deltaTime, Space.World);
        if (v == 1)
        {
            sprite.sprite = tankSprites[0];
            bulletEulAngle = new Vector3(0, 0, 0);
        }
        if (v == -1)
        {
            sprite.sprite = tankSprites[2];
            bulletEulAngle = new Vector3(0, 0, -180);
        }
    }
	
```

### 八、return 方法

> <1>return语句终止它所在的方法的执行，并将控制权返回给调用方法，另外，它还可以返回一个可选值。  如果方法为void类型，则可以省略return语句。

> <2>return语句后面可以是常量，变量，表达式，方法，也可以什么都不加。return语句可以出现在方法的任何位置。>
> 一个方法中也可以出现多个return，但只有一个会执行。当return语句后面什么都不加时，返回的类型为void。

### 九、产生随机位置的方法，以及检验随机位置是否符合要求的方法

```c#
private Vector2 CreatRandomPosition() //产生随机位置的方法；
    {
        while (true)
        {
            Vector2 randomPosition = new Vector2(Random.Range(-10, 10), Random.Range(-7.5f, 7.5f));

            if (WhetherHavePosition(randomPosition)) // 调用方法，检查产生的随机位置是否已经被使用；
            {
                return randomPosition;//如果没有就将该 位置坐标 返回出去；
            }
        }
    }

    private bool WhetherHavePosition(Vector2 randomPosition) //判断产生的随机位置是否已经被使用的方法；
    {
        for(int i = 0; i < alreadyIndex.Count; i++)
        {
            if (randomPosition == alreadyIndex[i])
            {
                return false; // 该位置已经被使用,直接将该位置返回；好像，就直接从这个方法里面跳了出去，不在执行后续；
            }
        }
        return true; ;//遍历完列表，没有相同的位置，就不会进入 if 条件，跳出 for 循环，返回 true，表示该位置没有被使用；
       
    }
	
```

### 九、变量后缀：“f” 等，不要乱加：

随机出整数：

``Vector2 randomPosition = new Vector2(Random.Range(-10, 11), Random.Range(-8, 9));``

 随机出小数：

`Vector2 randomPosition = new Vector2(Random.Range(-10f, 11f), Random.Range(-8f, 9f));`

> 不要随便乱加 f 后缀，会出事的，Random.Range(-8f, 9f)会随机出小数！
>
> 造成与预计结果不相符的情况*/

### 十、单例模式

```c#
//单例模式 private static PlayerManager instance; public static PlayerManager Instance//如果要在其它的方法里面 访问并修改该方法里面的变量，则要把这个方法写为 单例模式：    {        get        {            return instance;        }        set        {            instance = value;        }    }	
```

### 十一、其它场景的加载(通过一个场景进入其它场景)：

```c#
using UnityEngine.SceneManagement;SceneManager.LoadScene(1);  //要在Build Setting 面板 将场景拖进去，场景编号从0开始；SceneManager.LoadScene()；需要参数: 场景的编号 或 场景的名称(字符串);
```

### 十二、播放音效：

>  <1> 通过AudioSource组件来播放

```c#
private AudioSource moveAudio;  //定义组件public AudioClip[] moveAudioType; // [0]:Idel; [1]:Driving，获取音效； moveAudio = GetComponent<AudioSource>(); //得到组件 moveAudio.clip = moveAudioType[1];//音效赋值if (!moveAudio.isPlaying)//如果没有正在播放，就播放音效{   moveAudio.Play(); //播放音效；}	
```

> <2>直接播放：

```c#
public AudioClip bulletToBarrierAudio;//得到音效；AudioSource.PlayClipAtPoint(bulletToBarrierAudio, transform.position);//在该位置播放音效； 
```

> <3>直接使用AudioSource组件播放； 