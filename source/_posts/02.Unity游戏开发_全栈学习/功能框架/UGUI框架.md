---
title: Unity之UGUI框架
date: 2023-5-2 00:00:00
update: 2023-7-24 00:00:00
categories:
  - [Unity3D, 功能框架]
  - [Unity3D, GUI]
  - [Csharp]
tags: [Unity3D, 功能框架, GUI, Csharp, UGUI, Json, Litjson]
description: 本文主要介绍了Unity3D中，基于UGUI的UI框架的设计与实现，以及Json文件的解析使用和本地存储实现。
path: Unity3D/功能框架/UGUI/
---

# 前言

`UI`是一个游戏必不可少的一部分。对于一个游戏系统而言，它的`UI`也是多种多样的。经常地，因为要满足不同的需求，当前场景中的`UI`也会频繁切换。因此，为了更方便的管理这些`UI`的状态和行为，开发一个基于`UGUI`系统的**`UI`管理框架**。

# 一、需求分析

1. 进入游戏时，首先会加载**主 UI **，**主 UI **随着游戏的进入和退出而加载和销毁。
2. 通过一些按钮，我们可以打开其他的 UI 界面，同时主 UI 不会消失
3. 当前场景可以同时存在多级 UI ，只有处于顶级的 UI 才能被选中并进行操作
4. 除了主 UI 外的其他 UI 均可以在游戏场景未关闭下，手动关闭

# 二、框架设计

![UIFramework](http://imageshack.yuilexi.cn/Unity3D/功能框架/UGUI/UIFramework.svg)

# 三、功能实现

## 3.1 `UIPanel`

这一部分是用于描述 UI 面板属性的一些脚本。

### 3.1.1 `UIPanelType`脚本

这是一个枚举类，用于描述所有`UIPanel`的类型（以`UIPanel`的名字作为它的类型），下面只写了两个例子。

```C#
public enum UIPanelType
{
    MainMenuUIPanel,
    SettingUIPanel
}
```

### 3.1.2 `UIPanelInfo`脚本

该脚本下，只有一个类，用于描述`UIPanel`的类型以及对应模板文件的路径，并且使用属性器进行封装。将该类标记为**可序列化类**，以方便其他解析`Json`文件方法能够使用。

```C#
using System;

[Serializable]
public class UIPanelInfo
{
    private UIPanelType _type;
    private string _path;       //注意，脚本使用Rescources.load()动态加载，这里的文件路径不需要后缀

    public UIPanelInfo()
    { }
    public UIPanelInfo(UIPanelType type, string path)
    {
        _type = type;
        _path = path;
    }
    public UIPanelType Type
    {
        get { return _type; }
        set { _type = value; }
    }
    public string Path
    {
        get { return _path; }
        set { _path = value; }
    }
}
```

### 3.1.3 `BaseUIPanel`脚本

~~由于`BaseUIPanel`脚本需要挂载到所有面板对象上，因此需要继承`MonoBehaviour` 。~~ `BasePanel`里的方法用来描述所有面板共同的一些基本行为，面板的四个状态：进入场景`OnEnter`、暂停`OnPause`、继续`OnResume`（解除暂停）、退出场景`OnExit`，也是属于这个类的四个方法。

```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public abstract class BaseUIPanel : MonoBehaviour
{
    public virtual void OnEnter()
    {
    }
    public virtual void OnPause()
    {
    }
    public virtual void OnResume()
    {
    }
    public virtual void OnEixt()
    {
    }
}
```

## 3.2 `Manager`

`UIManager`类的该 UI 框架的核心，它负责的工作如下：

1. 自动更新`Json`文件中记载`UIPanel`的`UIPanelType`与路径
2. 获取所有的`UIPanel`信息，并将数据加载到脚本中
3. 使用字典储存所有`UIPanel`的游戏对象信息
4. 使用栈储存场景中已加载的`UIPanel`

### 3.2.1 单例模式

把`UIManager`做成单例模式，使其在游戏中，只有一个`UIManager`类去管理 UI 。代码如下：

```C#
private static UIManager s_instance;
//单例模式
public static UIManager Instance
{
    get
    {
        if (s_instance == null)
            s_instance = new UIManager();
        return s_instance;
    }
}
```

### 3.2.2 初始化并自动更新`Json`信息

这里使用`Litjson`库来解析`Json`文件，把`Litjson.dll`文件添加到对应路径下，并且在代码编辑器中添加对应的引用。脚本中引入命名空间，如下：

```C#
using LitJson;
```

配置`UIpanel`预制件文件夹的路径以及`Json`文件路径，如下

```C#
//存放 UIPanel 预制件的文件夹路径
private readonly string _basePahtUIPanelPrefabFolder = Application.dataPath + @"/Resources/UIPanelPrefab/";
//存放UIPanel信息的Json文件的路径
private readonly string _jsonFolderPath = Application.dataPath + @"/Json/UIJson/";
private readonly string _jsonFileName = "UIPanelInfo.json";
```

并且创建一个列表，用来获取所有的`UIPanelInfo`数据，其中也包括未加载到场景中的

```C#
private List<UIPanelInfo> _uIPanelInfos;//该列表是存放所有的UIPanelInfo信息，包括未加载到游戏场景中的
```

构造一个方法：传入`Json`路径和数据，并且将数据写入`Json`文件，如下：

```C#
private void WriteToJsonFile<T>(string folderPath, string fileName, T t)
{
    string json = JsonMapper.ToJson(t);
    //如果文件夹不存在，就创建文件夹
    if (!Directory.Exists(folderPath))
        Directory.CreateDirectory(folderPath);
    //如果文件不存在，就创建文件
    if (!File.Exists(folderPath + fileName))
        File.Create(folderPath + fileName).Dispose();
    //将数据写入到文件中
    File.WriteAllText(folderPath + fileName, json);
}
```

构造一个方法：传入`Json`路径，将对应路径下的`Json`文件中的数据读取到内存（脚本）中，如下：

```C#
private List<T> ReadFromJsonFIle<T>(string folderPath, string fileName) where T : class
{
    if (!Directory.Exists(folderPath))
        Directory.CreateDirectory(folderPath);
    if (!File.Exists(folderPath + fileName))
        File.WriteAllText(folderPath + fileName, "[]");
    List<T> t = JsonMapper.ToObject<List<T>>(File.ReadAllText(folderPath + fileName));
    return t;
}
```

构造一个方法：自动更新 `Json`文件，并获取全部的模板信息，代码如下：

```C#
private void InitUIPanelInfo()
{
    //先获取当前Json文件中的UIPanelInfo数据
    _uIPanelInfos = ReadFromJsonFIle<UIPanelInfo>(_jsonFolderPath, _jsonFileName);
    //创建UIPanel的文件夹对象
    if (!Directory.Exists(_basePahtUIPanelPrefabFolder))
        Directory.CreateDirectory(_basePahtUIPanelPrefabFolder);
    DirectoryInfo directoryInfo = new DirectoryInfo(_basePahtUIPanelPrefabFolder);

    //遍历文件夹下的每一个prefab文件。
    //如果当前UIPanelInfo列表中有对应类型的模板信息，就更新路径；没有，就添加对应信息到UIPanelInfo列表
    foreach (FileInfo fileInfo in directoryInfo.GetFiles("*prefab"))
    {
        //将对应UIPanel模板的名字转换为UIPanel类型
        UIPanelType type = (UIPanelType)Enum.Parse(typeof(UIPanelType), fileInfo.Name.Replace(".prefab", ""));
        string path = @"UIPanelPrefab/" + Convert.ToString(type); //基址+对应模板文件名，组成完整地址（不要后缀）

        //尝试在列表中寻找UIpanelInfo对象，如果有，则返回对应的对象；如果没有，则返回null
        UIPanelInfo uIPanelInfo = _uIPanelInfos.TrySearchUIPanel(type);

        if (uIPanelInfo == null)    //UIPanel不在该List中
        {
            uIPanelInfo = new UIPanelInfo(type, path);
            _uIPanelInfos.Add(uIPanelInfo);
        }
        else //UIPanel在该List中,更新path值
        {
            uIPanelInfo.Path = path;
        }
    }
    WriteToJsonFile<List<UIPanelInfo>>(_jsonFolderPath, _jsonFileName, _uIPanelInfos); //将更新后的模板信息写入Json文件中去
    AssetDatabase.Refresh(); //刷新资源
}
```

并且在`UIManager`的构造方法中调用`InitUIPanelInfo()`方法，以便于在该管理类创建时，就进行`Json`文件的更新。

```C#
public UIManager()
{
    InitUIPanelInfo();
}
```

### 3.2.3 实例化游戏`UIPanel`对象

首先，先获取当前场景的`Canvas`的 `Transform`组件，如下：

```C#
private Transform _canvasTransform;//存放当前场景中的Canvas对象的Transform属性

//获取Transform
public Transform CanvasTransform
{
    get
    {
        if (_canvasTransform == null)
            _canvasTransform = GameObject.Find("Canvas").transform;
        return _canvasTransform;
    }
    set { _canvasTransform = value; }
}
```

但是上述存在一个问题，使用`GameObject.Find("Canvas")`方法，性能很低，会占用更多的时间。改进方法中包含`set{}`部分，后续会讲解原因。

构造一个方法：传入`UIPanel`的类型，就能返回对应的`Panel`游戏对象，如下：

```C#
public BaseUIPanel GetUIPanel(UIPanelType uIPanelType)
{
    if (_uIPanelInfos == null)
        return null;

    string path = _uIPanelInfos.TrySearchUIPanel(uIPanelType).Path;

    #region 可能会出现的错误，实际开发时，可不用添加这块代码

    if (path == null)
        throw new Exception("找不到该UIPanelType的Prefab");
    if (Resources.Load(path) == null)
        throw new Exception("找不到该UIPanelType的Prefab");

    #endregion 可能会出现的错误，实际开发时，可不用添加这块代码

    GameObject insUIPanel = GameObject.Instantiate(Resources.Load(path)) as GameObject; //创建游戏实例对象
    insUIPanel.transform.SetParent(CanvasTransform, false); //设置父级对象
    return insUIPanel.GetComponent<BaseUIPanel>(); //返回BaseUIPanel脚本
}
```

### 3.2.4 保存当前场景中的 UIPanel 对象

一般情况下，不同的`UI`是先打开的后关闭，后打开的先关闭，这符合**栈**的性质。因此使用栈来存放当前的已打开的`UIPanel`对象。

首先，创建一个栈的字段，用于存放已打开的`UIPanel`对象，如下：

```C#
private Stack<BaseUIPanel> _currentUIPanels;//存放当前场景中已加载的UIPanel对象
```

入栈算法：构造一个方法，将对应的`UIPanel`压入栈中，并调用`OnEnter`方法

```C#
public void PushUIPanel(UIPanelType uIPanelType)
{
    //如果当前栈为空
    if (_currentUIPanels == null)
        _currentUIPanels = new Stack<BaseUIPanel>();
    //如果当前栈不为空，就把栈顶的UIPanel暂停
    if (_currentUIPanels.Count > 0)
    {
        BaseUIPanel topUIPanel = _currentUIPanels.Peek();
        topUIPanel.OnPause();
    }
    BaseUIPanel newUIPanel = GetUIPanel(uIPanelType);
    _currentUIPanels.Push(newUIPanel);
    newUIPanel.OnEnter();
}
```

出栈算法：构造一个方法，输入顶级`UIPanel`的类型，就能弹出对应的`UIPanel`，并调用`OnExit`方法：

```c#
public void PopUIPanel()
{
    //如果当前栈为空，就返回
    if (_currentUIPanels == null)
        return;
    //如果当前栈中没有UIPanel，就返回
    if (_currentUIPanels.Count <= 0)
        return;

    //弹出栈顶的UIPanel，并调用OnExit方法
    BaseUIPanel topUIPanel = _currentUIPanels.Pop();
    topUIPanel.OnEixt();
    //如果当前栈不为空，就把栈顶的UIPanel恢复
    if (_currentUIPanels.Count == 0)
        return;
    topUIPanel = _currentUIPanels.Peek();
    topUIPanel.OnResume();
}
```

### 3.2.5 初始化并删除场景已存在的`UIPanel`

如果场景中残留有旧的`UIPanel`，那么就先删除当前场景中所有的`UIPanel`，然后再加载`UIManager`，以便于向场景中添加新的`UI`。

```c#
private void CheckUIPanelWhenGameBegin()
{
    for (int i = 0;CanvasTransform.childCount> i;)
    {
        GameObject.DestroyImmediate(CanvasTransform.GetChild(i).gameObject);
    }
}
```

> 注意：上述方法中，for 循环并没有将循环因子`i`进行自增处理，这是由于`CanvasTransform.childCount`是在动态变化的，因此我们判断的条件是`CanvasTransform.childCount<=0`，终止循环。

并在构造方法中添加这个方法，以便于在游戏运行开始就执行该方法。

```c#
private UIManager()
{
    InitUIPanelInfo(); //初始化UIPanelInfo
    CheckUIPanelWhenGameBegin();
}
```

## 3.3 `UIManagerLoader`脚本

这个类负责在游戏开始的时候加载所有需要的游戏配置。在该 UI 框架里，`UIManagerLoader`负责在游戏刚开始运行时，提供给`Manager`类中的`Canvas`信息，并加载主菜单面板`MainMenuPanel`。由于这个类需要挂在场景中的物体上，所以需要继承自`MonoBehaviour`。代码如下：

```c#
//先把当前场景中的Canvas对象的Transform属性赋值给UIManager的CanvasTransform属性
//游戏一开始就加载MainMenuUIPanel
private void Awake()
{
    UIManager.Instance.CanvasTransform = this.transform;
    UIManager.Instance.PushUIPanel(UIPanelType.MainMenuUIPanel);
}
```

在[3.2.3 实例化游戏`UIPanel`对象](#3-2-3-实例化游戏UIPanel对象)中，我们提到了使用`GameObject.Find("Canvas")`方法寻找到当前的 UI 画布，但是`GameObject.Find("")`的性能较低，可能会占用较长的时间。因此，改进方法中包含`set{}`部分，那么可以在`UIManagerLoader`脚本中，向`Manager`类传入对应的画布对象，能更快的获取到`Canvas`对象，此举需要将该脚本挂载到 `Canvas` 游戏对象上。

## 3.4 完善`BaseUIPanel`类

### 3.4.1 获取`CanvasGroup`

```c#
private CanvasGroup _canvasGroup;
private void Awake()
{
    _canvasGroup = GetComponent<CanvasGroup>();
    if (_canvasGroup == null)
        _canvasGroup = gameObject.AddComponent<CanvasGroup>();
}
```

然后完善四个方法：

```c#
/// <summary>
/// 打开时需要执行的方法
/// </summary>
public void OnEnter()
{
    this.gameObject.SetActive(true);
    _canvasGroup.blocksRaycasts = true;
    _canvasGroup.alpha = 1;
}

/// <summary>
/// 暂停时需要执行的方法
/// </summary>
public void OnPause()
{
    _canvasGroup.blocksRaycasts = false;
    _canvasGroup.alpha = 1;
}

/// <summary>
/// 重新启动时需要执行的方法
/// </summary>
public void OnResume()
{
    this.gameObject.SetActive(true);
    _canvasGroup.blocksRaycasts = true;
    _canvasGroup.alpha = 1;
}

/// <summary>
/// 关闭时需要执行的方法
/// </summary>
public void OnEixt()
{
    this.gameObject.SetActive(false);
    Destroy(this.gameObject, 0.5f);
}
```

## 3.5 扩展

### 3.5.1 列表操作的扩展

```c#
public static class ListExtesion
{
    /// <summary>
    /// 尝试在列表中查找指定的元素
    /// </summary>
    /// <param name="uIPanelInfos">当前操作的列表</param>
    /// <param name="uIPanelType">定的元素</param>
    /// <returns>如果列表中由指定的元素，就返回该元素；如果没有，就返回null</returns>
    public static UIPanelInfo TrySearchUIPanel(this List<UIPanelInfo> uIPanelInfos, UIPanelType uIPanelType)
    {
        foreach (UIPanelInfo uIPanelInfo in uIPanelInfos)
        {
            if (uIPanelInfo.Type == uIPanelType)
                return uIPanelInfo;
        }
        return null;
    }
}
```

# 四、事件响应

首先创建新的文件夹`UIEvents`，然后再该文件夹下创建对应的脚本。例如：对于`MainMenuUIPanel`面板，创建`MainMenuUIPanel`类，并继承自`BaseUIPanel`。并对父类`BaseUIPanel`中的虚方法进行重写和调用，并构造该面板独有的事件响应函数。

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class MainMenuUIPanel : BaseUIPanel
{
    public override void OnEnter()
    {
        base.OnEnter();
        Debug.Log("MainMenuUIPanel OnEnter");
    }
    public override void OnPause()
    {
        base.OnPause();
        Debug.Log("MainMenuUIPanel OnPause");
    }
    public override void OnResume()
    {
        base.OnResume();
        Debug.Log("MainMenuUIPanel OnResume");
    }
    public override void OnEixt()
    {
        base.OnEixt();
        Debug.Log("MainMenuUIPanel OnEixt");
    }
    //打开设置界面的按钮
    public void OnSettingClick()
    {
        UIManager.Instance.PushUIPanel(UIPanelType.SettingUIPanel);
    }
}
```

然后将此脚本，挂载到对应的游戏对象上，然后再对应的按钮上设置点击响应事件。

![Panel添加脚本](http://imageshack.yuilexi.cn/Unity3D/功能框架/UGUI/Panel添加脚本.png)

![添加响应事件](http://imageshack.yuilexi.cn/Unity3D/功能框架/UGUI/添加响应事件.png)

# README

## 完整代码

{% folding 完整代码  %}

- UIFramework

  - UIPanel

    - UIPanelType.cs

      ```c#
      /// <summary>
      /// 枚举，用于标识UIPanel的类型
      /// </summary>
      public enum UIPanelType
      {
          MainMenuUIPanel,
          SettingUIPanel,
      }
      ```

    - UIPanelInfo.cs

      ```c#
      using System;

      /// <summary>
      /// 描述UIPanel的信息，包括类型和路径
      /// </summary>
      [Serializable]
      public class UIPanelInfo
      {
          private UIPanelType _type;
          private string _path;       //注意，脚本使用Rescources.load()动态加载，这里的文件路径不需要后缀

          public UIPanelInfo()
          { }

          public UIPanelInfo(UIPanelType type, string path)
          {
              _type = type;
              _path = path;
          }
          public UIPanelType Type
          {
              get { return _type; }
              set { _type = value; }
          }
          public string Path
          {
              get { return _path; }
              set { _path = value; }
          }
      }
      ```

    - BasePanel.cs

      ```c#
      using System.Collections;
      using System.Collections.Generic;
      using UnityEngine;

      /// <summary>
      /// 该类需要挂载到每一个Panel的预制件上，因此继承MonoBehaviour
      /// </summary>
      public abstract class BaseUIPanel : MonoBehaviour
      {
          private CanvasGroup _canvasGroup;
          private void Awake()
          {
              _canvasGroup = GetComponent<CanvasGroup>();
              if (_canvasGroup == null)
                  _canvasGroup = gameObject.AddComponent<CanvasGroup>();
          }
          /// <summary>
          /// 打开时需要执行的方法
          /// </summary>
          public virtual void OnEnter()
          {
              this.gameObject.SetActive(true);
              _canvasGroup.blocksRaycasts = true;
              _canvasGroup.alpha = 1;
          }

          /// <summary>
          /// 暂停时需要执行的方法
          /// </summary>
          public virtual void OnPause()
          {
              _canvasGroup.blocksRaycasts = false;
              _canvasGroup.alpha = 1;
          }

          /// <summary>
          /// 重新启动时需要执行的方法
          /// </summary>
          public virtual void OnResume()
          {
              this.gameObject.SetActive(true);
              _canvasGroup.blocksRaycasts = true;
              _canvasGroup.alpha = 1;
          }

          /// <summary>
          /// 关闭时需要执行的方法
          /// </summary>
          public virtual void OnEixt()
          {
              this.gameObject.SetActive(false);
              Destroy(this.gameObject, 0.5f);
          }
      }
      ```

  - Manager

    - UIManager.cs

      ```c#
      using LitJson;
      using System;
      using System.Collections;
      using System.Collections.Generic;
      using System.IO;
      using UnityEditor;
      using UnityEngine;
      using UnityEngine.UIElements;

      public class UIManager
      {
          #region 单例模式
          //单例模式
          private static UIManager s_instance;
          public static UIManager Instance
          {
              get
              {
                  if (s_instance == null)
                      s_instance = new UIManager();
                  return s_instance;
              }
          }
          #endregion 单例模式

          #region 文件路径
          //存放 UIPanel 预制件的文件夹路径
          private readonly string _basePahtUIPanelPrefabFolder = Application.dataPath + @"/Resources/UIPanelPrefab/";
          //存放UIPanel信息的Json文件的路径
          private readonly string _jsonFolderPath = Application.dataPath + @"/Json/UIJson/";
          private readonly string _jsonFileName = "UIPanelInfo.json";
          #endregion 文件路径

          private List<UIPanelInfo> _uIPanelInfos;   //该列表是存放所有的UIPanelInfo信息，包括未加载到游戏场景中的

          private Transform _canvasTransform; //存放当前场景中的Canvas对象的Transform属性

          private Stack<BaseUIPanel> _currentUIPanels; //使用栈，存放当前场景中的所有UIPanel
            //获取Transform
            public Transform CanvasTransform
            {
                get
                {
                    if (_canvasTransform == null)
                        _canvasTransform = GameObject.Find("Canvas").transform;
                    return _canvasTransform;
                }
                set
                {
                    if (value.name == "Canvas")
                        _canvasTransform = value;
                }
            }

            private UIManager()
            {
                InitUIPanelInfo(); //初始化UIPanelInfo
                CheckUIPanelWhenGameBegin();
            }
            /// <summary>
            /// 传入路径和对应的数据，把该数据写入到对应路径的Json文件里
            /// </summary>
            /// <param name="folderPath">文件夹路径</param>
            /// <param name="fileName">文件名</param>
            /// <param name="t">写入的数据</param>
            /// <typeparam name="T">数据类型</typeparam>
            private void WriteToJsonFile<T>(string folderPath, string fileName, T t)
            {
                string json = JsonMapper.ToJson(t);
                //如果文件夹不存在，就创建文件夹
                if (!Directory.Exists(folderPath))
                    Directory.CreateDirectory(folderPath);
                //如果文件不存在，就创建文件
                if (!File.Exists(folderPath + fileName))
                    File.Create(folderPath + fileName).Dispose();
                //将数据写入到文件中
                File.WriteAllText(folderPath + fileName, json);
            }

            /// <summary>
            /// 传入文件的路径，就能读取到该文件的数据
            /// </summary>
            /// <param name="folderPath">文件夹路径</param>
            /// <param name="fileName">文件名</param>
            /// <typeparam name="T"></typeparam>
            private List<T> ReadFromJsonFIle<T>(string folderPath, string fileName) where T : class
            {
                if (!Directory.Exists(folderPath))
                    Directory.CreateDirectory(folderPath);
                if (!File.Exists(folderPath + fileName))
                    File.WriteAllText(folderPath + fileName, "[]");
                List<T> t = JsonMapper.ToObject<List<T>>(File.ReadAllText(folderPath + fileName));
                return t;
            }

            /// <summary>
            /// 自动更新Json文件，并获取到所有的UIPanelInfo数据
            /// </summary>
            private void InitUIPanelInfo()
            {
                //先获取当前Json文件中的UIPanelInfo数据
                _uIPanelInfos = ReadFromJsonFIle<UIPanelInfo>(_jsonFolderPath, _jsonFileName);

                //创建UIPanel的文件夹对象
                if (!Directory.Exists(_basePahtUIPanelPrefabFolder))
                    Directory.CreateDirectory(_basePahtUIPanelPrefabFolder);
                DirectoryInfo directoryInfo = new DirectoryInfo(_basePahtUIPanelPrefabFolder);

                //遍历文件夹下的每一个prefab文件。
                //如果当前UIPanelInfo列表中有对应类型的模板信息，就更新路径；没有，就添加对应信息到UIPanelInfo列表
                foreach (FileInfo fileInfo in directoryInfo.GetFiles("*.prefab"))
                {
                    //将对应UIPanel模板的名字转换为UIPanel类型
                    UIPanelType type = (UIPanelType)Enum.Parse(typeof(UIPanelType), fileInfo.Name.Replace(".prefab", ""));
                    string path = @"UIPanelPrefab\" + Convert.ToString(type); //基址+对应模板文件名，组成完整地址（不要后缀）

                    //尝试在列表中寻找UIpanelInfo对象，如果有，则返回对应的对象；如果没有，则返回null
                    UIPanelInfo uIPanelInfo = _uIPanelInfos.TrySearchUIPanel(type);

                    if (uIPanelInfo == null)    //UIPanel不在该List中
                    {
                        uIPanelInfo = new UIPanelInfo(type, path);
                        _uIPanelInfos.Add(uIPanelInfo);
                    }
                    else //UIPanel在该List中,更新path值
                    {
                        uIPanelInfo.Path = path;
                    }
                }
                WriteToJsonFile<List<UIPanelInfo>>(_jsonFolderPath, _jsonFileName, _uIPanelInfos); //将更新后的模板信息写入Json文件中去
                AssetDatabase.Refresh(); //刷新资源
            }
            /// <summary>
            /// 根据对应的UIPanel类型，创建游戏实例对象，并返回该对象的BaseUIPanel脚本
            /// </summary>
            /// <param name="uIPanelType">UIPanlel的类型</param>
            /// <returns></returns>
            /// <exception cref="Exception"></exception>
            public BaseUIPanel GetUIPanel(UIPanelType uIPanelType)
            {
                if (_uIPanelInfos == null)
                    return null;

                string path = _uIPanelInfos.TrySearchUIPanel(uIPanelType).Path;

                #region 可能会出现的错误，实际开发时，可不用添加这块代码

                if (path == null)
                    throw new Exception("找不到该UIPanelType的Prefab");
                if (Resources.Load(path) == null)
                    throw new Exception("找不到该UIPanelType的Prefab");

                #endregion 可能会出现的错误，实际开发时，可不用添加这块代码

                GameObject insUIPanel = GameObject.Instantiate(Resources.Load(path)) as GameObject; //创建游戏实例对象
                insUIPanel.transform.SetParent(CanvasTransform, false); //设置父对象
                return insUIPanel.GetComponent<BaseUIPanel>(); //返回BaseUIPanel脚本
            }

            /// <summary>
            /// 将对应的UIPanel压入栈中，并调用OnEnter方法
            /// </summary>
            /// <param name="uIPanelType"></param>
            public void PushUIPanel(UIPanelType uIPanelType)
            {
                //如果当前栈为空
                if (_currentUIPanels == null)
                    _currentUIPanels = new Stack<BaseUIPanel>();
                //如果当前栈不为空，就把栈顶的UIPanel暂停
                if (_currentUIPanels.Count > 0)
                {
                    BaseUIPanel topUIPanel = _currentUIPanels.Peek();
                    topUIPanel.OnPause();
                }
                BaseUIPanel newUIPanel = GetUIPanel(uIPanelType);
                _currentUIPanels.Push(newUIPanel);
                newUIPanel.OnEnter();
            }

            /// <summary>
            /// 输入顶级UIPanel的类型，就能弹出对应的UIPanel，并调用OnExit方法
            /// </summary>
            /// <param name="uIPanelType"></param>
            public void PopUIPanel()
            {
                //如果当前栈为空，就返回
                if (_currentUIPanels == null)
                    return;
                //如果当前栈中没有UIPanel，就返回
                if (_currentUIPanels.Count <= 0)
                    return;

                //弹出栈顶的UIPanel，并调用OnExit方法
                BaseUIPanel topUIPanel = _currentUIPanels.Pop();
                topUIPanel.OnEixt();
                //如果当前栈不为空，就把栈顶的UIPanel恢复
                if (_currentUIPanels.Count == 0)
                    return;
                topUIPanel = _currentUIPanels.Peek();
                topUIPanel.OnResume();
            }

            /// <summary>
            /// 摧毁场景中原来已经存在的UIPanel
            /// </summary>
            private void CheckUIPanelWhenGameBegin()
            {
                for (int i = 0; CanvasTransform.childCount > i;)
                {
                    GameObject.DestroyImmediate(CanvasTransform.GetChild(i).gameObject);
                }
            }
        }
      ```

    - UIManagerLoader.cs

      ```c#
      using UnityEngine;

      public class UIManagerLoader : MonoBehaviour
      {
          //先把当前场景中的Canvas对象的Transform属性赋值给UIManager的CanvasTransform属性
          //游戏一开始就加载MainMenuUIPanel
          private void Awake()
          {
              UIManager.Instance.CanvasTransform = this.transform;
              UIManager.Instance.PushUIPanel(UIPanelType.MainMenuUIPanel);
          }
      }
      ```

  - Extension

    - ListExtension.cs

      ```c#
      using System.Collections;
      using System.Collections.Generic;

      public static class ListExtesion
      {
          /// <summary>
          /// 尝试在列表中查找指定的元素
          /// </summary>
          /// <param name="uIPanelInfos">当前操作的列表</param>
          /// <param name="uIPanelType">定的元素</param>
          /// <returns>如果列表中由指定的元素，就返回该元素；如果没有，就返回null</returns>
          public static UIPanelInfo TrySearchUIPanel(this List<UIPanelInfo> uIPanelInfos, UIPanelType uIPanelType)
          {
              foreach (UIPanelInfo uIPanelInfo in uIPanelInfos)
              {
                  if (uIPanelInfo.Type == uIPanelType)
                      return uIPanelInfo;
              }
              return null;
          }
      }
      ```

  - UIEvents

    - MainMenuUIPanel.cs

      ```c#
      using System.Collections;
      using System.Collections.Generic;
      using UnityEngine;

      public class MainMenuUIPanel : BaseUIPanel
      {
          public override void OnEnter()
          {
              base.OnEnter();
              Debug.Log("MainMenuUIPanel OnEnter");
          }
          public override void OnPause()
          {
              base.OnPause();
              Debug.Log("MainMenuUIPanel OnPause");
          }
          public override void OnResume()
          {
              base.OnResume();
              Debug.Log("MainMenuUIPanel OnResume");
          }
          public override void OnEixt()
          {
              base.OnEixt();
              Debug.Log("MainMenuUIPanel OnEixt");
          }

          public void OnSettingClick()
          {
              UIManager.Instance.PushUIPanel(UIPanelType.SettingUIPanel);
          }
      }

      ```

    - SettingUIPanel.cs

      ```c#
      using System.Collections;
      using System.Collections.Generic;
      using UnityEngine;

      public class SettingUIPanel : BaseUIPanel
      {
          public override void OnEnter()
          {
              base.OnEnter();
              Debug.Log("SettingUIPanel OnEnter");
          }
          public override void OnPause()
          {
              base.OnPause();
              Debug.Log("SettingUIPanel OnPause");
          }
          public override void OnResume()
          {
              base.OnResume();
              Debug.Log("SettingUIPanel OnResume");
          }
          public override void OnEixt()
          {
              base.OnEixt();
              Debug.Log("SettingUIPanel OnEixt");
          }
          public void OnCloseClick()
          {
              UIManager.Instance.PopUIPanel();
          }
      }
      ```

{% endfolding %}

## 更新日志

{% folding 更新日志 %}

{% timeline 更新日志,orange %}

<!-- timeline 2023-5-8 -->

1.  添加了**事件响应**功能，以及将`BaseUIPanel`类调整为抽象类，因此`UIPanel`上将会挂载`BaseUIPanel`的子类，而不是`BaseUIPanel`。
2.  更新了 **框架设计** 部分的图示。

<!-- endtimeline -->

{% endtimeline %}

{% endfolding %}
