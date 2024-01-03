---
title: Unity组件-Transform
data: 2023-5-11 00:00:00
update: 2023-7-24 00:00:00
categories:
  - [Unity3D, Unity组件]
tags: [Unity3D, Unity组件, Transform变换]
description: Unity3D中Transform组件的使用
image_path: Unity3D/Unity组件/组件-Transform/
---

# Transform-组件

**`Transform`** 用于存储**游戏对象**的位置、旋转、缩放和父级状态，因此非常重要。游戏对象将始终附加 `Transform` 组件。如果没有变换组件，则无法移除变换或创建游戏对象。

## `Transform`组件

`Transform` 组件确定对象在场景中的**位置和姿态**。**每个游戏对象都有一个变换**。

![Transform组件视图](http://imageshack.yuilexi.cn/Unity3D/Unity组件/组件-TransformTransform组件视图.png)

## 属性

|     属性     | 功能                                                                                                                         |
| :----------: | :--------------------------------------------------------------------------------------------------------------------------- |
| **Position** | 相对于父级（本地坐标系），在 X、Y 和 Z 坐标中的位置。                                                                        |
| **Rotation** | 相对于父级，围绕 X、Y 和 Z 轴的旋转，以度为单位。                                                                            |
|  **Scale**   | 沿 X、Y 和 Z 轴的变换比例。值“1”是原始大小（导入对象的大小）。选择值旁边的链接图标以切换比例缩放。比例缩放按比例调整刻度值。 |

> 注意：每个`Transform`组件面板中的**Position**，指的是相对于父级的位置，一般游戏对象在场景中的位置是世界坐标，因此需要将子级的**Position**以及对应的每一级父级的**Position**相加，才能得到**世界坐标**。而脚本中的`Transform`对象下的**Position**属性，就是世界坐标。这一点很重要
>
> 为了说明这一点，请看下面例子。
>
> 首先设置父级坐标为 `(-2,0,0)` ，子级坐标 `(0,0,0)` ，然后打印出子级 `Transform` 脚本对象的**Position**属性。
>
> ![父级坐标](http://imageshack.yuilexi.cn/Unity3D/Unity组件/组件-Transform父级坐标.png)
>
> ![子级坐标](http://imageshack.yuilexi.cn/Unity3D/Unity组件/组件-Transform子级坐标.png)
>
> 将下面测试脚本挂载到子级上
>
> ```c#
> using UnityEngine;
>
> public class Test : MonoBehaviour
> {
>     // Start is called before the first frame update
>     void Start()
>     {
>         Debug.Log(this.transform.position);
>     }
> }
> ```
>
> 打印的结果为：
>
> ![输出的结果](http://imageshack.yuilexi.cn/Unity3D/Unity组件/组件-Transform输出的结果.png)

## 编辑转换

`Transform` 在 X、Y 和 Z 轴的 3D 空间中或在 X 和 Y 轴的 2D 空间中进行操作。在 Unity 中，这些轴分别由红色、绿色和蓝色表示。

![左手坐标系](http://imageshack.yuilexi.cn/Unity3D/Unity组件/组件-Transform左手坐标系.png)

在场景中，您可以使用移动、旋转和缩放工具修改`Transform`。这些工具位于 Unity 编辑器的左上角。

![转换](http://imageshack.yuilexi.cn/Unity3D/Unity组件/组件-Transform转换-平移.png)

## Parenting——父级

`Parenting` 是使用 Unity 时要了解的最重要的概念之一。当游戏对象是另一个游戏对象的**父级**，**子游戏对象将完全按照其父游戏对象的方式移动、旋转和缩放**。

可以把 `Parenting` 想象成手臂和身体之间的关系：每当你的身体移动时，你的手臂也会随之移动。子对象也可以有自己的子对象，依此类推。所以你的手可以被视为你手臂的“孩子”，然后每只手都有几根手指，等等。任何对象都可以有多个子对象，但只能有一个父对象。这些多层次的父子关系构成了转换*层次结构*。

层次结构最顶端的对象（即层次结构中唯一没有父对象的对象）称为**根**。

可以通过将**层次结构视图中**的任何游戏对象拖动到另一个游戏对象上来创建父级。这将在两个游戏对象之间创建父子关系。

## 使用 `Transform` 的小提示

- 在父级转换时，在添加子级之前将父级的位置设置为 <0，0，0> 非常有用。这意味着子项的本地坐标将与全局坐标相同，从而更容易确保子项处于正确的位置。
- 如果您正在使用**刚体**，对于物理仿真，请务必阅读[刚体](https://docs.unity3d.com/Manual/class-Rigidbody.html)组件参考页面上的 Scale 属性。
- 您可以从首选项（**菜单：Unity >首选项**）更改变换轴（和其他 UI 元素）的颜色，然后选择**颜色和键**面板）。
- 更改比例会影响子变换的位置。例如，将父项缩放为 （0，0，0） 会将所有子项定位为相对于父项的 （0，0，0）。

# Transform——脚本

### 描述

对象的位置、旋转和缩放。场景中的每个对象都有一个变换。 它用于存储和操作对象的位置，旋转和缩放。 每个变换都可以有一个父级，它允许您分层应用位置、旋转和缩放。这是在“层次结构”窗格中看到的层次结构。 它们还支持**枚举器**，因此您可以使用以下方法循环遍历子项：

```c#
using UnityEngine;

public class Example : MonoBehaviour
{
    void Start()
    {
        Transform transform = gameObject.GetComponent<Transform>();
        foreach (Transform child in transform)
        {
            child.position += Vector3.up * 10.0f;
        }
    }
}
```

## 常用属性

下面只罗列在游戏开发中，最常用的属性，完整属性列表请参看[Unity - Scripting API: Transform (unity3d.com)](https://docs.unity3d.com/ScriptReference/Transform.html)官方文档

| [childCount](https://docs.unity3d.com/ScriptReference/Transform-childCount.html)       | 父级 `Transform` 有的子级数。（注意：这里是一级子级数）。 |
| -------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| [localPosition](https://docs.unity3d.com/ScriptReference/Transform-localPosition.html) | 相对于父级的位置。                                        |
| [localRotation](https://docs.unity3d.com/ScriptReference/Transform-localRotation.html) | 相对于父级的旋转。                                        |
| [localScale](https://docs.unity3d.com/ScriptReference/Transform-localScale.html)       | 相对于父级的缩放。                                        |
| [parent](https://docs.unity3d.com/ScriptReference/Transform-parent.html)               | 当前 `Transform` 的父级的 `Transform` 对象。              |
| [position](https://docs.unity3d.com/ScriptReference/Transform-position.html)           | 世界坐标空间位置。                                        |
| [rotation](https://docs.unity3d.com/ScriptReference/Transform-rotation.html)           | 一个四元数，用于存储在世界空间中的旋转。                  |

## 常用公共方法

下面只罗列在游戏开发中，最常用的公共方法，完整公共方法列表请参看[Unity - Scripting API: Transform (unity3d.com)](https://docs.unity3d.com/ScriptReference/Transform.html)官方文档

| [DetachChildren](https://docs.unity3d.com/ScriptReference/Transform.DetachChildren.html) | 取消所有孩子的父母。如果要在不销毁子层次结构的情况下销毁层次结构的根，则很有用。                                                |
| ---------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| [Find](https://docs.unity3d.com/ScriptReference/Transform.Find.html)                     | **按名称查找**子项并将其返回。注意：只会在一级子级中查找。                                                                      |
| [GetChild](https://docs.unity3d.com/ScriptReference/Transform.GetChild.html)             | 按索引返回转换子项。                                                                                                            |
| [IsChildOf](https://docs.unity3d.com/ScriptReference/Transform.IsChildOf.html)           | 返回一个布尔值，该值指示转换是否为给定转换的子级。 如果此转换是子转换，则为真，深度子项（子项的子项）或与此转换相同，否则为假。 |
| [Rotate](https://docs.unity3d.com/ScriptReference/Transform.Rotate.html)                 | 使用 Transform.Rotate 以多种方式旋转游戏对象。旋转通常以欧拉角而不是四元数的形式提供。                                          |
| [RotateAround](https://docs.unity3d.com/ScriptReference/Transform.RotateAround.html)     | 围绕在世界坐标中通过点的轴按角度旋转变换。                                                                                      |
| [**SetParent**](https://docs.unity3d.com/ScriptReference/Transform.SetParent.html)       | **设置转换的父级**。                                                                                                            |
| [Translate](https://docs.unity3d.com/ScriptReference/Transform.Translate.html)           | 沿平移方向和距离移动变换。（世界坐标的封装）                                                                                    |

### `Find()` 方法

下面进行测试：

![Find方法测试-1](http://imageshack.yuilexi.cn/Unity3D/Unity组件/组件-TransformFind方法测试-1.png)

挂载的脚本如下：

```c#
using UnityEngine;

public class Test : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        Transform transform = gameObject.GetComponent<Transform>();
        Transform transform1 = transform.Find("www");
        if (transform1 == null)
        {
            Debug.Log("null");
        }
        else
        {
            Debug.Log(transform1.gameObject.name);
        }
    }
}
```

最后的控制台输出的结果为：

![Find方法测试-结果](http://imageshack.yuilexi.cn/Unity3D/Unity组件/组件-TransformFind方法测试-结果.png)

> 总结：上面的结果可以看出， `Find()` 方法，并不能找到二级子级

### `SetParent（）` 方法

声明

```c#
public void SetParent(Transform parent);
public void SetParent(Transform parent, bool worldPositionStays);
```

说明：

- `parent` ：要使用的父转换。
- `worldPositionStays`
  - ` true` ：修改相对于父级的位置、缩放和旋转，**以使对象保持与以前相同的世界空间位置、旋转和缩放**。
  - `false` ：设置相对于父级的位置、缩放和旋转，为默认值。（此举可能会改变世界坐标系空间的状态）

# README

## 更新日志

{% folding 更新日志 %}

{% timeline 更新日志,orange %}

<!-- timeline 2023-5-12 -->

1.  添加了 `Find()` 方法的测试
<!-- endtimeline -->

<!-- timeline 2023-5-11 -->

1.  创建文档，介绍 Transform 组件的使用
<!-- endtimeline -->

{% endtimeline %}

{% endfolding %}
