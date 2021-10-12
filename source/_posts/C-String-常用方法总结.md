---
title: C# String 常用方法总结
date: 2020-09-26 06:18:41
tags: C#
cover: https://z3.ax1x.com/2021/10/12/5eyHhT.jpg
category: 编程语言
---



##### [1]字符串复制

`String.Copy(str)`：参数str为要复制的字符串，它会返回一个与该字符串相等的字符串；

`SreStr.CopyTo(StartOfIndex, DestStr, StartOfDestStr, CopyLen)`：被要复制的字符串实例调用，它可以实现复制其中某一部分到目标字符串的指定位置;

| StartOfIndex       | DestStr      | StartOfDestStr   | CopyLen      |
| ------------------ | ------------ | ---------------- | ------------ |
| 要被复制的开始下标 | 要粘贴的数组 | 从该位置开始粘贴 | 要复制的长度 |

##### [2]字符串比较

`String.Compare(str1, str2)`：

返回1 ： str1大于str2；0 ： str1等于str2；-1 ： str1小于str2

`String.CompareTo(string value)`：该实例与value的值进行比较。

返回：如果string大于value则返回1，如果string小于value则返回-1，如果两个相等则返回0。 

`String.CompareOrdinal(str1, str2)`：是将整个字符串每5个字符（10个字节）分成一组，然后逐个比较，找到第一个不相同的ASCII码后退出循环，并且求出两者的ASCII码的差。这个方法比其他方法都要快。

`String.Equals(string value)`：用于比较两个字符串是否相等；

##### [3]字符串查找

`String.Contains(Findstr)`：`Findstr`为要查找的字符或字符串；功能：指定字符串是否包含一个字串，返回值的bool类型。

`String.IndexOf(Findstr)`：`Findstr`为要查找的字符或字符串；查找FindStr在字符串中第一次出现的位置，返回值为第一次出现的下标，没有找到则返回-1。

`String.LastIndexOf(FindStr)`：`Findstr`为要查找的字符或字符串；查找FindStr在字符串中最后一次出现的位置，返回值为最后一次出现的下标，没有找到则返回-1。

##### [4]字符串截取

`String.SubString(StartIndex)`：StartIndex-要进行截取的开始下标；保留字符串中，下标从StartIndex开始后面的全部字符串。

`String.SubString(StartIndex, Len)`：StartIndex-要开始截取的下标，Len-要截取的长度；保留字符串中下标从StartIndex开始后面的Len个长度的字符串。

##### [5]字符串分割

`String.Split(SplitChar)`：SplitChar为分割的字符；将字符串以SplitChar为分割点进行分割，它的返回值是一个字符串数组。

##### [6]字符串合并

`String.Concat(str1, str2, …., strn)`：将n个字符串连接，中间没有连接符 ；

String.Join(SplitChar,array)：SplitChar-合并后的分隔符，array为要合并的字符串数组；将字符串数组合并成一个字符串，每个元素之间用SplitChar隔开。

##### [7]字符串替换

`String.Replace(SreStr, ChangeStr)`：参数：SreStr—要替换的原字符串，ChangeStr—用来替换的字符串。用ChangeStr来替换字符串中的SreStr。

注意：替换并不会影响原字符串，需要新建string对象来接收替换之后的新数组；

##### [8]字符串插入与填充

`String.Insert(index, str)`：index是需要插入的位置，str是要插入的字符； 

`String.PadRight(Len, char)`：Len—字符串的总长度，char—用来填充的字符。如果字符串长度都不够Len，则在结尾用char来填充 

`String.PadLeft(Len, char)`：Len—字符串的总长度，char—用来填充的字符。如果字符串长度不够Len，则在开始处用char来填充。

##### [9]字符串删除

`String.Trim()`：删除字符串中开始和结尾处的空格。

`string.Trim(char[])`：char[]为想要去掉的字符数组，去掉字符串开始处和结尾处char[]中的所有字符。str.Trim('@','!','%','^','&')；

`String.Remove(startIndex)`：startIndex—需要删除的起始位置，该方法是将位置后的所有字符全部删除。

String.Remove(startIndex, Len)：Len是指需要删除的长度，从startIndex位置开始删除Len个字符。

##### [10]字符串大小写转换

`String.ToLower()`：将字符串转化为小写形式。
`String.ToUpper()`：将字符串转化为大写形式。

