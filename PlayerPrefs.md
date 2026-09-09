<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>Unity PlayerPrefs 数据持久化课件</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: "Microsoft YaHei", sans-serif;
        }
        body {
            background-color: #1e1e1e;
            color: #e0e0e0;
            padding: 30px;
            line-height: 1.7;
        }
        h1 {
            color: #4fc1ff;
            margin-bottom: 20px;
            font-size: 28px;
        }
        h2 {
            color: #7cd86b;
            margin: 30px 0 15px;
            font-size:22px;
            border-bottom: 1px solid #333;
            padding-bottom:8px;
        }
        h3 {
            color: #ffd270;
            margin:20px 0 10px;
            font-size:18px;
        }
        p {
            margin:10px 0;
            font-size:16px;
        }
        .note-box{
            background-color:#2d2d2d;
            padding:16px;
            border-radius:8px;
            margin:12px 0;
        }
        pre{
            background-color:#111111;
            color:#dcdcdc;
            padding:16px;
            border-radius:6px;
            overflow-x:auto;
            margin:12px 0;
            font-family:Consolas, monospace;
            font-size:14px;
        }
        .keyword{
            color:#569cd6;
        }
        .string{
            color:#ce9178;
        }
        .comment{
            color:#6a9955;
        }
        .func{
            color:#dcdcaa;
        }
    </style>
</head>
<body>
    <h1>Unity PlayerPrefs 数据持久化</h1>

    <h2>概念</h2>
    <div class="note-box">
        <p>把程序运行在内存中的临时数据，保存到磁盘（文件、注册表、数据库等），程序关闭、游戏退出之后，数据不会消失；下次启动程序，可以重新读取出来继续使用。</p>
        <p>把数据从内存传到硬盘的过程就是<strong>数据持久化</strong>。</p>
    </div>

    <h2>PlayerPrefs</h2>
    <div class="note-box">
        <p>是Unity提供用于存储读取玩家数据的公共类</p>
    </div>
<pre>
public class PlayerPrefs
{
    public PlayerPrefs();
    public static void DeleteAll();
    public static void DeleteKey(string key);
    public static float GetFloat(string key, float defaultValue);
    public static float GetFloat(string key);
    public static int GetInt(string key, int defaultValue);
    public static int GetInt(string key);
    public static string GetString(string key, string defaultValue);
    public static string GetString(string key);
    public static bool HasKey(string key);
    public static void SetFloat(string key, float value);
    public static void SetInt(string key, int value);
    public static void SetString(string key, string value);
}
</pre>

    <h2>基本方法</h2>
    <h3>存储相关</h3>
    <div class="note-box">
        <p>PlayerPrefs的数据存储类似于键值对存储，一个键对应一个值</p>
        <p>提供了存储三种数据的方法 int float string</p>
        <p>值：int float string 对应3种API</p>
    </div>
<pre>
PlayerPrefs.SetInt("myAge",18);
PlayerPrefs.SetFloat("myHeight",18.8f);
PlayerPrefs.SetString("myName","summer");
</pre>
    <div class="note-box">
        <p>直接调用Set相关方法 只会把数据存到内存里</p>
        <p>当游戏结束时 Unity会自动把数据存到硬盘中</p>
        <p>如果游戏不是正常结束的，而是崩溃 数据是不会存到硬盘中的</p>
    </div>
    <div class="note-box">
        <p>只要调用该方法 就会马上存到硬盘中</p>
        <p><span class="func">PlayerPrefs.Save();</span></p>
    </div>
    <div class="note-box">
        <p>PlayerPrefs是有局限性的 它只能存3种类型的数据</p>
        <p>如果你想要存储别的类型的数据 只能降低精度或者上升精度来进行存储</p>
    </div>
<pre>
bool sex = true;
PlayerPrefs.SetInt("sex", sex ? 1 : 0);
</pre>
    <div class="note-box">
        <p>如果不同类型用同一键名进行存储 会进行覆盖</p>
    </div>
<pre>
PlayerPrefs.SetFloat("myAge",20.2f);
</pre>

    <h3>读取相关</h3>
    <div class="note-box">
        <p>注意 运行时 只要你Set了对应键值对</p>
        <p>即使你没有马上存储Save在本地</p>
        <p>也能够读取取出信息</p>
    </div>
    <div class="note-box">
        <p>GetInt/Float/String 一共有两种重载</p>
        <p>第二个参数 默认值 对于我们的作用</p>
        <p>就是在得到没有的数据的时候 就可以用它来进行基础数据的初始化</p>
    </div>
<pre>
//int
int age = PlayerPrefs.GetInt("myAge");
print(age);
//前提是：如果找不到myAge对应的值 就会返回函数的第二个参数 默认值
age = PlayerPrefs.GetInt("myAge", 100);
print(age);

//float
float height = PlayerPrefs.GetFloat("myHeight", 1000f);
print(height);
</pre>
    <div class="note-box">
        <p>//判断数据是否存在</p>
    </div>
<pre>
if (PlayerPrefs.HasKey("myName"))
{
    print("存在myName对应的键值对数据");
}
</pre>

    <h3>删除数据</h3>
    <div class="note-box">
        <p>删除指定键值对</p>
    </div>
<pre>
PlayerPrefs.DeleteKey("myAge");
</pre>
    <div class="note-box">
        <p>删除所有数据</p>
    </div>
<pre>
PlayerPrefs.DeleteAll();
</pre>


    <h2>项目：基于反射+PlayerPrefs通用持久化管理器</h2>
    <div class="note-box">
        <p>Unity 基于反射 + PlayerPrefs 实现的通用数据持久化单例管理器</p>
        <p>原理：利用反射自动扫描类的所有 public 字段，递归处理基础类型、List、Dictionary、自定义实体类，全部存入 PlayerPrefs。</p>
        <h3>核心架构</h3>
        <p>1.单例模式：PlayerPrefsDataMgr 全局唯一实例，整个游戏共用这一套存储逻辑</p>
        <p>2.对外2个核心方法：</p>
        <p>　SaveData(object data, string keyName)：保存对象</p>
        <p>　LoadData(Type type, string keyName)：读取对象，返回重建好的对象</p>
        <p>3.内部递归辅助方法：</p>
        <p>　SaveValue(object value, string keyName)：保存单个值（递归，支持嵌套List/自定义类）</p>
        <p>　LoadValue(Type fieldType, string keyName)：读取单个值（递归重建对象）</p>
    </div>
<pre>
using System;
using System.Collections;
using System.Collections.Generic;
using System.Reflection;
using UnityEngine;
/// <summary>
/// PlayerPrefs数据管理类 统一管理数据的存储和读取
/// </summary>
public class PlayerPrefsDataMgr
{
    //私有构造函数 ＋ 静态自身实例，保证整个程序这个类只有一个对象
    private static PlayerPrefsDataMgr instance = new PlayerPrefsDataMgr();
    public static PlayerPrefsDataMgr Instance//对外获取实例
    {
        get
        {
            return instance;
        }
    }
    private PlayerPrefsDataMgr()//私有构造函数 ：禁止外部new这个类
    {
    }
    /// <summary>
    /// 存储数据
    /// </summary>
    /// <param name="data">数据对象</param>
    /// <param name="keyName">数据对象的唯一key 自己控制</param>
    public void SaveData(object data, string keyName)
    {
        //就是要通过 Type 得到传入数据对象的所有的 字段
        //然后结合 PlayerPrefs来进行存储
        #region 第一步 获取传入数据对象的所有字段
        Type dataType = data.GetType();
        //得到所有的字段
        FieldInfo[] infos = dataType.GetFields();
        #endregion
        #region 第二步 自己定义一个key的规则 进行数据存储
        //我们存储都是通过PlayerPrefs来进行存储的
        //保证key的唯一性 我们就需要自己定一个key的规则
        //我们自己定一个规则
        // keyName_数据类型_字段类型_字段名
        #endregion
        #region 第三步 遍历这些字段 进行数据存储
        string saveKeyName = "";
        FieldInfo info;
        for (int i = 0; i < infos.Length; i++)
        {
            //对每一个字段 进行数据存储
            //得到具体的字段信息
            info = infos[i];
            //通过FieldInfo可以直接获取到 字段的类型 和字段的名字
            //字段的类型 info.FieldType.Name
            //字段的名字 info.Name;
            //要根据我们定的key的拼接规则 来进行key的生成
            //Player1_PlayerInfo_Int32_age
            saveKeyName = keyName + "_" + dataType.Name +
                "_" + info.FieldType.Name + "_" + info.Name;
            //现在得到了Key 按照我们的规则
            //接下来就要来通过PlayerPrefs来进行存储
            //如何获取值
            //info.GetValue(data)
            //封装了一个方法 专门来存储值 
            SaveValue(info.GetValue(data), saveKeyName);
        }
        PlayerPrefs.Save();
        #endregion
    }
    private void SaveValue(object value, string keyName)
    {
        //直接通过PlayerPrefs来进行存储了
        //就是根据数据类型的不同 来决定使用哪一个API来进行存储
        //PlayerPrefs只支持3种类型存储 
        //判断 数据类型 是什么类型 然后调用具体的方法来存储
        Type fieldType = value.GetType();
        //类型判断
        //是不是int
        if (fieldType == typeof(int))
        {
            Debug.Log("存储int" + keyName);
            PlayerPrefs.SetInt(keyName, (int)value);
        }
        else if (fieldType == typeof(float))
        {
            Debug.Log("存储float" + keyName);
            PlayerPrefs.SetFloat(keyName, (float)value);
        }
        else if (fieldType == typeof(string))
        {
            Debug.Log("存储string" + keyName);
            PlayerPrefs.SetString(keyName, value.ToString());
        }
        else if (fieldType == typeof(bool))
        {
            Debug.Log("存储bool" + keyName);
            //自己顶一个存储bool的规则
            PlayerPrefs.SetInt(keyName, (bool)value ? 1 : 0);
        }
        //如何判断 泛型类的类型呢
        //通过反射 判断 父子关系
        //这相当于是判断 字段是不是IList的子类
        //IList是List的父类
        //如果fieldType继承了IList 则fieldType被认为是List（目前我们只见到IList被List继承）
        else if (typeof(IList).IsAssignableFrom(fieldType))
        {
            Debug.Log("存储List" + keyName);
            //父类装子类
            IList list = value as IList;
            //先存储 数量 
            PlayerPrefs.SetInt(keyName, list.Count);
            int index = 0;
            foreach (object obj in list)
            {
                //存储具体的值
                SaveValue(obj, keyName + index);
                ++index;
            }
        }
        //判断是不是Dictionary类型 通过Dictionary的父类来判断
        else if (typeof(IDictionary).IsAssignableFrom(fieldType))
        {
            Debug.Log("存储Dictionary" + keyName);
            //父类装自来
            IDictionary dic = value as IDictionary;
            //先存字典长度
            PlayerPrefs.SetInt(keyName, dic.Count);
            //遍历存储Dic里面的具体值
            //用于区分 表示的 区分 key
            int index = 0;
            foreach (object key in dic.Keys)
            {
                SaveValue(key, keyName + "_key_" + index);
                SaveValue(dic[key], keyName + "_value_" + index);
                ++index;
            }
        }
        //基础数据类型都不是 那么可能就是自定义类型
        else
        {
            SaveData(value, keyName);
        }
    }
    /// <summary>
    /// 读取数据
    /// </summary>
    /// <param name="type">想要读取数据的 数据类型Type</param>
    /// <param name="keyName">数据对象的唯一key 自己控制</param>
    /// <returns></returns>
    public object LoadData(Type type, string keyName)
    {
        //不用object对象传入 而使用 Type传入
        //主要目的是节约一行代码（在外部）
        //假设现在你要 读取一个Player类型的数据 如果是object 你就必须在外部new一个对象传入
        //现在有Type的 你只用传入 一个Type typeof(Player) 然后我在内部动态创建一个对象给你返回出来
        //达到了 让你在外部 少写一行代码的作用
        //根据你传入的类型 和 keyName
        //依据你存储数据时  key的拼接规则 来进行数据的获取赋值 返回出去
        //根据传入的Type 创建一个对象 用于存储数据
        object data = Activator.CreateInstance(type);
        //要往这个new出来的对象中存储数据 填充数据
        //得到所有字段
        FieldInfo[] infos = type.GetFields();
        //用于拼接key的字符串
        string loadKeyName = "";
        //用于存储 单个字段信息的 对象
        FieldInfo info;
        for (int i = 0; i < infos.Length; i++)
        {
            info = infos[i];
            //key的拼接规则 一定是和存储时一模一样 这样才能找到对应数据
            loadKeyName = keyName + "_" + type.Name +
                "_" + info.FieldType.Name + "_" + info.Name;
            //有key 就可以结合 PlayerPrefs来读取数据
            //填充数据到data中 
            info.SetValue(data, LoadValue(info.FieldType, loadKeyName));
        }
        return data;
    }
    /// <summary>
    /// 得到单个数据的方法
    /// </summary>
    /// <param name="fieldType">字段类型 用于判断 用哪个api来读取</param>
    /// <param name="keyName">用于获取具体数据</param>
    /// <returns></returns>
    private object LoadValue(Type fieldType, string keyName)
    {
        //根据 字段类型 来判断 用哪个API来读取
        if (fieldType == typeof(int))
        {
            return PlayerPrefs.GetInt(keyName, 0);
        }
        else if (fieldType == typeof(float))
        {
            return PlayerPrefs.GetFloat(keyName, 0);
        }
        else if (fieldType == typeof(string))
        {
            return PlayerPrefs.GetString(keyName, "");
        }
        else if (fieldType == typeof(bool))
        {
            //根据自定义存储bool的规则 来进行值的获取
            return PlayerPrefs.GetInt(keyName, 0) == 1 ? true : false;
        }
        else if (typeof(IList).IsAssignableFrom(fieldType))
        {
            //得到长度
            int count = PlayerPrefs.GetInt(keyName, 0);
            //实例化一个List对象 来进行赋值
            //用了反射中双A中 Activator进行快速实例化List对象
            IList list = Activator.CreateInstance(fieldType) as IList;
            for (int i = 0; i < count; i++)
            {
                //目的是要得到 List中泛型的类型 
                list.Add(LoadValue(fieldType.GetGenericArguments()[0], keyName + i));
            }
            return list;
        }
        else if (typeof(IDictionary).IsAssignableFrom(fieldType))
        {
            //得到字典的长度
            int count = PlayerPrefs.GetInt(keyName, 0);
            //实例化一个字典对象 用父类装子类
            IDictionary dic = Activator.CreateInstance(fieldType) as IDictionary;
            Type[] kvType = fieldType.GetGenericArguments();
            for (int i = 0; i < count; i++)
            {
                dic.Add(LoadValue(kvType[0], keyName + "_key_" + i),
                         LoadValue(kvType[1], keyName + "_value_" + i));
            }
            return dic;
        }
        else
        {
            return LoadData(fieldType, keyName);
        }
        return null;
    }
}
</pre>
</body>
</html>
