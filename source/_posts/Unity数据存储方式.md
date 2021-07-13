---
title: Unity数据存储方式
date: 2020-07-13 14:10:09
tags: unity
cover: ../image/savesate.jpg
category: unity
---

Unity数据存储方式：

> 使用PlayerPrefs，进行数据存储，该种方法使用方便，在不同的平台都能正常使用；

1. PlayerPrefs 游戏存档：

   在游戏会话中储存和访问游戏存档。这个是持久化数据储存，比如保存游戏记录。

2. Static Functions 静态函数

   - DeleteAll：从游戏存档中删除所有key和数据，请谨慎使用。

   - PlayerPrefs.DeleteKey ：删除键和它对应的值； ```PlayerPrefs.DeleteKey("KeyName");`

   - PlayerPrefs.HasKey: 使用有键,如果key在游戏存档中存在，返回true。

     ```PlayerPrefs.HasKey("KillName");```

   - PlayerPrefs.Save :保存; 写入所有修改参数到硬盘。

     默认Unity在程序退出时保存参数。在游戏崩溃或过早退出时，想要写入游戏存档到游戏中合理的“检查点”； 这个函数将写入数据到硬盘，游戏可能有稍微的停顿，因此它不建议在实际游戏期间调用。

   - PlayerPrefs.GetFloat 获得浮点数

     PlayerPrefs.GetInt 获得整数

     PlayerPrefs.GetString 获得字符串

     如果存在，返回游戏存档文件中key对应的数值；如果不存在，将返回默认值。（默认值可以  不设置）。

     `Value = PlayerPrefs.GetFloat("Health", 0);`

     ``` Value = PlayerPrefs.GetInt("Score", 0);```

     ``` Value = PlayerPrefs.GetString("Name", "No Name");```

   - PlayerPrefs.SetFloat 设置浮点数； 设置由key确定的浮点数值;

     ```PlayerPrefs.SetFloat("Health", 50.0F); ```

     ```PlayerPrefs.SetInt 设置整数；public static void SetInt(string key, int value);设置由key键确定的整数值。```

     ```PlayerPrefs.SetInt("Score", 20);```  

     ```PlayerPrefs.SetString 设置字符串；public static void SetString(string key, string value); 设置由key确定的字符串值。```

     ```PlayerPrefs.SetString("Name", _PlayerName);```

   - 

> 游戏数据存储——序列化

```c#
 private void Seve()
    {
        try
        {
            BinaryFormatter bf = new BinaryFormatter();
            //文件读写流的使用，创建数据文件；
            using (FileStream fs=File.Create(Application.persistentDataPath+"/GameData.data"))//文件路径+文件名；
            {
                /*为GameData里面的各项数据赋值*/
                data.IsFirstGame = isFirstGame;
                data.IsMusicOn = isMusicOn;
                data.SelectSkin = selectSkin;
                data.SkinUnlock = skinUnlock;
                data.Diamond = diamond;
                data.BestScoreArray = bestScoreArray;
                
                bf.Serialize(fs, data);//数据序列化；
            }
        }
        catch (System.Exception e)
        {
            Debug.Log(e.Message);
            throw;
        }
    }
```

> 游戏数据的读取——反序列化

```c#
private void Read()
    {
        try
        {
            BinaryFormatter bf = new BinaryFormatter();
            using (FileStream fs=File .Open(Application.persistentDataPath + "/GameData.data",FileMode.Open))           
            {
                data = (GameData)bf.Deserialize(fs);//反序列化；将本地文件转为 GameData 数据；
            }
        }
        catch (System.Exception e)
        {
            Debug.Log(e.Message);

            /// 读取数据的过程不能抛出异常，
            //throw;
        }
    }
	GameData 游戏数据类：
```

>  通过序列化与反序列化存储数据；

```c#
[System.Serializable]//序列化标签；
public class GameData
{
    public static bool IsReStart;

    public bool IsFirstGame { get; set; }
    public bool IsMusicOn { set; get; }
    public bool[] SkinUnlock { set; get; }
    public int Diamond { set; get; }
    public int SelectSkin { get; set; }
    public int[] BestScoreArray { set; get; }
}

```

> 通过 Resources 文件夹读取XML文件.
>
> XML 文件要在 Resources

```c#
using System.Xml;
  TextAsset XMLAsset = Resources.Load<TextAsset>(PathDefine.RandomNameCfg);

        if (XMLAsset)///文件存在
        {
			///开始解析XML文件；
            XmlDocument doc = new XmlDocument();
            doc.LoadXml(XMLAsset.text);

            /// 获得根节点 root 下面的所有子节点
            XmlNodeList nodeList = doc.SelectSingleNode("root").ChildNodes;
            
            ///遍历所有子节点；获得各个子节点的ID和子节点里面的二级子节点
            foreach (XmlElement element in nodeList)
            {
                if (element.GetAttributeNode("ID") == null)///如果子节点的ID没有，就遍历下一个子节点
                    continue;
                int ID = Convert.ToInt32(element.GetAttributeNode("ID").InnerText);///每个节点的ID；

                foreach (XmlElement item in element.ChildNodes)///应该是从第一个ChildNode开始遍历；
															   ///不用放入XmlNodeList 也可以；
                {
                    switch (item.Name)///检查各子节点的名称；按名称获取信息；
                    {
                        case "surname":
                            firstName.Add(item.InnerText);
                            break;
                        case "man":
                            boyName.Add(item.InnerText);
                            break;
                        case "woman":
                            girlName.Add(item.InnerText);
                            break;                            
                    }
                }

            }
        }

```

