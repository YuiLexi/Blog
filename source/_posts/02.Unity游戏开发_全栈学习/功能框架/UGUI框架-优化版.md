---
title: Unity之UGUI框架(优化版)
date: 2023-5-16 00:00:00
update: 2023-7-24 00:00:00
categories:
  - [Unity3D, 功能框架]
  - [Unity3D, GUI]
  - [Csharp]
tags: [Unity3D, 功能框架, GUI, Csharp, UGUI, Json, Litjson]
description: 本文主要介绍了之前UIGUI框架的优化版，主要是对之前的框架进行了一些优化，使得框架更加的完善，更加的易用。
path: Unity3D/功能框架/UGUI/
---

# 前言

> 此文章主要是针对之前的 UGUI 框架，进行优化和改进。
>
> - [Unity 之 UGUI 框架 | 🪐 星空鸟 🪐 (yuilexi.cn)](https://blog.yuilexi.cn/2023/05/02/Unity3D/功能框架/UGUI框架/)
> - [Unity 之 UGUI 框架(优化版) | 🪐 星空鸟 🪐 (yuilexi.cn)](https://blog.yuilexi.cn/2023/05/16/Unity3D/功能框架/UGUI框架-优化版/)🔫🧬 当前位置 🧬

# 一、框架设计

由于之前的 UGUI 框架，采用的 UI 模板是 `Panel` 对象，并将所有的 `UI` 置于一个 `Canvas` 下，这样会对性能的影响很大。 `Canvas` 的[渲染模式](https://zhuanlan.zhihu.com/p/343524911)，简单说明就是， `Canvas` 会把所有子类的 `UI` 合并到一个 `Mesh` 里面去，然后再提交渲染后的 `UI` 数据，因此，当某一个子对象改变时，都要重新绘制`Mesh` ，这样会大大增加运算时间。

解决方案如下：

- 不用的 `UI` 直接使用独立的 `Canvas` 作为面板
- 资源使用动态加载，并且已关闭的 `UI` 面板不缓存
- `Canvas` 之间，尽量避免嵌套

![新版UI框架](https://imageshack.yuilexi.cn/Unity3D/功能框架/UGUI/新版UI框架.svg)

# 二、功能实现

## 2.1 `UIType`

这是一个枚举类，用于描述所有 `UICanvas` 的类型（以 `UICanvas` 的名字作为它的类型），下面只写了两个例子。

```c#
public enum UIType
{
    None,
    MainMenuCanvas,
    SettingsCanvas
}
```

要求：

- 添加一个 `None` 作为空类型
- 必须以 `Canvas` 结尾，
- `UI` 资源文件命名要和该枚举中一一对应

## 2.2 `UIlnfo`

该脚本下，只有一个类，用于描述 `UICanvas` 的类型以及对应模板文件的路径。将该类标记为**可序列化类**，以方便其他解析`Json`文件方法能够使用。

```c#
public class UIInfo
{
    private UIType _type;
    private string _path;
    public UIInfo()
    { }
    public UIInfo(UIType uiType, string path)
    {
        this._type = uiType;
        this._path = path;
    }
    public UIType Type { get => _type; }
    public string Path { get => _path; set => this._path = value; }
}
```

利用属性器进行封装， `UICanvas` 的路径信息可以更新，但是其类型不可以更新。

## 2.3 `BaseUI`

`BaseUI`里的方法用来描述所有面板共同的一些基本行为。面板的四个行为：进入场景`OnEnter`、暂停`OnPause`、继续`OnResume`（解除暂停）、退出场景`OnClose`，也是属于这个类的四个方法。

```c#
using UnityEngine;
public abstract class BaseUI : MonoBehaviour
{
    private CanvasGroup _canvasGroup;
    private UIType _type;
    public UIType Type { get => _type;protected set => _type = value; }
    protected virtual void Awake()
    {
        Init();
    }
    private void Init()
    {
        if (gameObject.GetComponent<Canvas>() == null)
        {
            gameObject.AddComponent<Canvas>();
        }
        _canvasGroup = gameObject.GetComponent<CanvasGroup>();
        if (_canvasGroup == null)
        {
            _canvasGroup = gameObject.AddComponent<CanvasGroup>();
        }
    }
    public virtual void OnEnter() { }
    public virtual void OnPause() { }
    public virtual void OnResume() { }
    public virtual void OnClose() { }
}
```

## 2.4 `UIManager`

`UIManager `类的该 UI 框架的核心，它负责的工作如下：

1. 自动更新 `Json` 文件中记载 `UICanvas` 的 `UIType` 与路径
2. 获取所有的 `UIPanel` 信息，并将数据加载到脚本中
3. 使用字典储存所有 `UIPanel` 的游戏对象信息
4. 使用栈储存场景中已加载的 `UIPanel`

### 2.4.1 单例模式

把`UIManager`做成单例模式，使其在游戏中，只有一个`UIManager`类去管理 UI 。代码如下：

```c#
private static UIManager _instance;
public static UIManager Instance
{
    get
    {
        if (_instance == null)
            _instance = new UIManager();
        return _instance;
    }
}
```

### 2.4.2 初始化并自动更新`Json`信息

这里使用`Litjson`库来解析`Json`文件，把`Litjson.dll`文件添加到`Plugins`下，并且在代码编辑器中添加对应的引用。脚本中引入命名空间，如下：

```c#
using LitJson;
```

配置`UIpanel`预制件文件夹的路径以及`Json`文件路径，如下

```c#
//存放 UIPanel 预制件的文件夹路径
private readonly string _canvasPrefabFolder = Application.dataPath + @"/Resources/UICanvasPrefab/";
//存放UIPanel信息的Json文件的路径
private readonly string _jsonFolderPath = Application.dataPath + @"/Json/UIJson/";
private readonly string _jsonFileName = "UIPanelInfo.json";
```

并且使用列表，用来获取所有的 `UIInfo` 数据，其中也包括未加载到场景中的 `UIInfo` 。代码如下：

```c#
private List<UIInfo> _uIInfoList;//该列表是存放所有的UIPanelInfo信息，包括未加载到游戏场景中的
```

构造一个方法：传入`Json`路径和数据，就能将数据写入`Json`文件，如下：

```c#
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

```c#
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

```c#
private void InitUIInfo()()
{
    //先获取当前Json文件中的UIPanelInfo数据
    _uIInfoList = ReadFromJsonFIle<UIInfo>(_jsonFolderPath, _jsonFileName);

    //创建UIPanel的文件夹对象
    if (!Directory.Exists(_canvasPrefabFolder))
        Directory.CreateDirectory(_canvasPrefabFolder);
    DirectoryInfo directoryInfo = new DirectoryInfo(_canvasPrefabFolder);

    //遍历文件夹下的每一个prefab文件。
    //如果当前UIPanelInfo列表中有对应类型的模板信息，就更新路径；没有，就添加对应信息到UIPanelInfo列表
    foreach (FileInfo fileInfo in directoryInfo.GetFiles("*Canvas.prefab"))
    {
        //将对应UIPanel模板的名字转换为UIPanel类型
        //UIType type = (UIType)Enum.Parse(typeof(UIType), fileInfo.Name.Replace(".prefab", ""));

        // 上面的代码不能处理"UIType中不存在对应类型"的情况，因此改为下面的代码(修改时间2023-6-23-21:58)
        UIType type;
        if (!Enum.TryParse(fileInfo.Name.Replace(".prefab", ""), out type))
        {
            Debug.LogError("UIManager.InitInfo() -> TryParse UIType Error!");
            continue;
        }
        string path = @"UICanvasPrefab/" + Convert.ToString(type); //基址+对应模板文件名，组成完整地址（不要后缀）

        //尝试在列表中寻找UIpanelInfo对象，如果有，则返回对应的对象；如果没有，则返回null
        UIInfo uIInfo = _uIInfoList.TrySearchUI(type);

        if (uIInfo == null)    //UIPanel不在该List中
        {
            uIInfo = new UIInfo(type, path);
            _uIInfoList.Add(uIInfo);
        }
        else //UIPanel在该List中,更新path值
        {
            uIInfo.Path = path;
        }
    }
    WriteToJsonFile<List<UIInfo>>(_jsonFolderPath, _jsonFileName, _uIInfoList); //将更新后的模板信息写入Json文件中去
    AssetDatabase.Refresh(); //刷新资源
}
```

并且在`UIManager`的构造方法中调用`InitUIPanelInfo()`方法，以便于在该管理类创建时，就进行`Json`文件的更新。

```c#
public UIManager()
{
    InitUIInfo()();
}
```

### 2.4.3 实例化游戏`Canvas`对象

首先，先获取当前场景的`UISysterm`的 `Transform`组件，如下：

```c#
private Transform _canvasTransform;////存放当前场景中的所有Canvas对象的父级对象的Transform属性
public Transform CanvasTransform
{
    get
    {
        if (_canvasTransform == null)
            _canvasTransform = GameObject.Find("UISysterm").transform;
        if (_canvasTransform == null)
        {
            _canvasTransform = new GameObject("UISysterm").transform;
            _canvasTransform.position = Vector3.zero;
        }
        return _canvasTransform;
    }
    set => _canvasTransform = value;
}
```

构造一个方法：传入`UI`的类型，就能返回对应的`Canvas`游戏对象，如下：

```C#
public BaseUI GetUIPanel(UIType type)
{
    if (_uIInfoList == null)
        return null;
    string path = _uIInfoList.TrySearchUIPanel(type).Path;

    #region 可能会出现的错误，实际开发时，可不用添加这块代码

    if (path == null)
        throw new Exception("找不到该UIPanelType的Prefab");
    if (Resources.Load(path) == null)
        throw new Exception("找不到该UIPanelType的Prefab");

    #endregion 可能会出现的错误，实际开发时，可不用添加这块代码

    GameObject insUIPanel = GameObject.Instantiate(Resources.Load(path)) as GameObject; //创建游戏实例对象
    insUIPanel.transform.SetParent(CanvasTransform, false); //设置父级对象
    return insUIPanel.GetComponent<BaseUI>();
}
```

### 2.4.4 保存当前场景中的 `UIPanel` 对象

一般情况下，不同的`UI`是先打开的后关闭，后打开的先关闭，这符合**栈**的性质。因此使用栈来存放当前的已打开的`Canvas`对象。

首先，创建一个栈的字段，用于存放已打开的`Canvas`对象，如下：

```c#
public void PushUIPanel(UIType type)
{
    //如果当前栈为空
    if (_currentUIPanels == null)
        _currentUIPanels = new Stack<BaseUI>();
    //如果当前栈不为空，就把栈顶的UIPanel暂停
    if (_currentUIPanels.Count > 0)
    {
        BaseUI topUIPanel = _currentUIPanels.Peek();
        topUIPanel.OnPause();
    }
    BaseUI newUIPanel = GetUIPanel(type);
    _currentUIPanels.Push(newUIPanel);
    newUIPanel.OnEnter();
}
```

出栈算法：构造一个方法，输入顶级`UI`的类型，就能弹出对应的`Canvas`，并调用`OnExit`方法：

```c#
public void PopUIPanel(UIType type)
{
    //如果当前栈为空，就返回
    if (_currentUIPanels == null)
        return;
    //如果当前栈中没有UIPanel，就返回
    if (_currentUIPanels.Count <= 0)
        return;

    //弹出栈顶的UIPanel，并调用OnExit方法
    BaseUI topUIPanel = _currentUIPanels.Pop();
    if (topUIPanel.Type != type)
    {
        Debug.LogError("弹出的UIPanel类型不匹配");
        return;
    }
    topUIPanel.OnClose();
    //如果当前栈不为空，就把栈顶的UIPanel恢复
    if (_currentUIPanels.Count == 0)
        return;
    topUIPanel = _currentUIPanels.Peek();
    topUIPanel.OnResume();
}
```

### 2.4.5 初始化并删除场景旧的`UIPanel`

如果场景中残留有旧的`Canvas`，那么就先删除当前场景中所有的`Canvas`，然后再加载`UIManager`，以便于向场景中添加新的`UI`。

```c#
private void CheckUICanvasWhenGameBegin()
{
    foreach (Transform child in CanvasTransform)
    {
        GameObject.DestroyImmediate(child.gameObject);
    }
}
```

并在构造方法中添加这个方法，以便于在游戏运行开始就执行该方法。

```c#
private UIManager()
{
    InitUIPanelInfo(); //初始化UIPanelInfo
    CheckUICanvasWhenGameBegin();
}
```

## 2.5 `UILoader`

这个类负责在游戏开始的时候加载所有需要的游戏配置。在该 UI 框架里，`UILoader`负责在游戏刚开始运行时，提供给`Manager`类中的`Canvas`信息，并加载主菜单面板`MainMenuCanvas`。由于这个类需要挂在场景中的物体上，所以需要继承自`MonoBehaviour`。代码如下：

```c#
public class UILoader : MonoBehaviour
{
    private void Awake()
    {
        UIManager.Instance.CanvasTransform = this.transform;
        UIManager.Instance.PushUIPanel(UIType.BackgroundCanvas);
        UIManager.Instance.PushUIPanel(UIType.MainMenuCanvas);
    }
}
```

## 2.6 完善`BaseUIPanel`类

```c#
public virtual void OnEnter()
{
    _canvasGroup.alpha = 1;
    _canvasGroup.blocksRaycasts = true;
}
public virtual void OnPause()
{
    _canvasGroup.blocksRaycasts = false;
}
public virtual void OnResume()
{
    _canvasGroup.blocksRaycasts = true;
}
public virtual void OnClose()
{
    _canvasGroup.blocksRaycasts = false;
    _canvasGroup.alpha = 0;
    gameObject.SetActive(false);
    Destroy(this.gameObject);
}
```

# 三、拓展

## 3.1 列表操作的扩展

```c#
public static class ListExtesion
{
    /// <summary>
    /// 尝试在列表中查找指定的元素
    /// </summary>
    /// <param name="uIPanelInfos">当前操作的列表</param>
    /// <param name="uIPanelType">定的元素</param>
    /// <returns>如果列表中由指定的元素，就返回该元素；如果没有，就返回null</returns>
    public static UIInfo TrySearchUI(this List<UIInfo> uIInfos, UIType uIType)
    {
        foreach (UIInfo uIInfo in uIInfos)
        {
            if (uIInfo.Type == uIType)
                return uIInfo;
        }
        return null;
    }
}
```

# 四、事件响应

与旧版的基本一样。

# README

## 完整代码

## 更新日志

{% folding 更新日志 %}

{% timeline 更新日志,orange %}

<!-- timeline 2023-6-23 -->

1. 在上述 2.4.2 中，“将文件名的字符串转化为对应枚举值”进行优化，能够处理“转换后的枚举值不存在”的问题

<!-- endtimeline -->

<!-- timeline 2023-6-9 -->

1. 在列表的扩展中，修复一个错误。
1. 修复图片显示异常的问题

<!-- endtimeline -->

<!-- timeline 2023-5-16 -->

1. 完成对 UGUI 优化版的整理

<!-- endtimeline -->

{% endtimeline %}

{% endfolding %}
