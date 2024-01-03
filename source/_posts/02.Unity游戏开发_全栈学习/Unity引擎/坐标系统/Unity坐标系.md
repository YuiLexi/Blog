---
title: Unity坐标系
date: 2023-5-28 00:00:00
update: 2023-7-24 00:00:00
tags: [Unity3D, Unity引擎, 坐标系统]
categories:
  - [Unity3D, Unity引擎, 坐标系统]
description: Unity坐标系，包括世界坐标系、本地坐标系、屏幕坐标系、GUI坐标系、瓦片地图坐标系。
mathjax: true
katex: true
image_path: Unity3D/Unity引擎/坐标系系/Unity坐标系/
---

# 前言

这一篇文章，主要介绍 Unity 中的各种坐标系，并且介绍如何使用 C# 脚本，实现不同坐标系之间的转换。

> 快速导航
>
> [Unity 组件-Transform | 🪐 星空鸟 🪐 (yuilexi.cn)](https://blog.yuilexi.cn/2023/05/11/Unity3D/Unity组件/组件-Transforms/)

# 一、世界坐标系

在 Unity 中，世界坐标系是指一个三维空间中的坐标系，可以用来描述场景中物体的位置和方向。它是由 X 轴、Y 轴和 Z 轴组成的，其中 X 轴表示水平方向（从左向右），Y 轴表示垂直方向（从下向上），Z 轴表示深度方向（从屏幕内向外）。默认情况下，原点位于场景的中心点。

在 Unity 中，所有的对象都有它们自己的坐标系，包括世界坐标系和本地坐标系。世界坐标系是所有对象的公共坐标系，因此可以用来描述整个场景中的对象位置和方向。

如果你想在代码中使用**世界坐标系**，可以使用 **Transform 组件的 position 属性**，它表示对象在世界坐标系中的位置。

但是，**Transform 组件** 面板中的 Position ，确表示的是本地（局部）坐标。

> 需要注意的是，Unity 中的坐标系默认使用左手坐标系。这意味着 X 轴向右，Y 轴向上，Z 轴向屏幕内。如果你想使用右手坐标系，可以在项目设置中进行修改。

![左手系和右手系](https://imageshack.yuilexi.cn/Unity3D/Unity引擎/坐标系系/Unity坐标系/左手系和右手系.svg)

在世界坐标系中，还有两个其他的描述对象空间状态的属性：旋转与缩放。这两个属性在 `Transform` 组件面板中，表示的是相对于父级的状态。如果想要获得世界坐标系中的状态，需要将每一级父对象的属性叠加（注意：这里是叠加，而不是相加）。

# 二、本地坐标系

在 Unity 中，本地坐标系（Local Coordinates System）或局部坐标系（Local Coordinate System）是指相对于某个物体自身的坐标系。每个物体都有其自己的本地坐标系，该坐标系以物体的位置、旋转和缩放为基准。

在 `Transform` 组件面板中的 `Position` 属性，表示的是该对象的相对于父级的本地坐标系的坐标位置。

> 一个游戏对象，想要获得世界坐标系，那么就需要将所有的父级以及祖级的本地坐标系相加。如果一个游戏对象在场景中没有父级，那么它的 `Transform` 中属性值，就是世界坐标下的属性值。

**旋转**（Rotation）：物体的本地坐标系的旋转是基于物体的转换。例如，当一个物体绕 Y 轴旋转时，其本地坐标系的 X 和 Z 轴也会随之改变方向。

**缩放**（Scale）：物体的本地坐标系的缩放取决于物体的缩放变换。如果一个物体被缩放了，那么其本地坐标系的轴的长度也会相应地进行缩放。

# 三、屏幕坐标系

![屏幕坐标系](https://imageshack.yuilexi.cn/Unity3D/Unity引擎/坐标系系/Unity坐标系/屏幕坐标系.svg)

屏幕坐标系就是把屏幕看作一个坐标系，从左下角开始计算，也就是(0,0)，而右上角则是`(Screen.widht-1,Screen.height-1)`，所以又叫做像素坐标系。可以通过下面的代码获得屏幕的分辨率。

```c#
public static void GetScreenCoordinate()
{
    Debug.Log("当前窗口的分辨率为：" + Screen.width + "X" + Screen.height);
    Debug.Log("当前屏幕的分辨率为：" + Screen.currentResolution);
}
```

鼠标位置坐标就是属于屏幕坐标系，通过屏幕坐标和世界坐标互转，可得到鼠标在 Unity3D 中的实际交互位置，然后就可以通过逻辑做出反馈。

获取鼠标的当前坐标，代码逻辑为：

```c#
public static void GetMouseCoordinate()
{
    // 获取鼠标在屏幕坐标系中的位置
    Vector3 mouseScreenPosition = Input.mousePosition;
    // 输出鼠标的屏幕坐标和世界坐标
    Debug.Log("Mouse Screen Position: " + mouseScreenPosition);
}
```

# 四、视口坐标系

![视口坐标系](https://imageshack.yuilexi.cn/Unity3D/Unity引擎/坐标系系/Unity坐标系/视口坐标系.svg)

视口坐标系可以理解为单位化的屏幕坐标系，该坐标系计算方式和屏幕坐标系类似，只不过把其参数标准化了，更加适用于比例计算。左下角为 `(0,0)` 右上角为 `(1,1)` 。

将屏幕坐标单位化后，就可以挣脱分辨率不同的限制，通过相对位置来确定在屏幕中的位置。

# 五、瓦片地图坐标系

在 2D 游戏对象中，我们可以通过创建 `TileMap` ，轻松地搭建出不同地游戏场景地图，因此，就会存在一种特殊的坐标系——**瓦片地图坐标系**。

由于瓦片的网格大小可以进行设置，因此，每个瓦片在世界坐标系中位置不尽相同。但是可以确定的是，**瓦片地图坐标系**的坐标，是一个整型的三元数。

我们可以通过自定义一个 C# 脚本笔刷，来显示当前的瓦片坐标。在 `Asset/TileMap/Custom Brush Scripts/Coordinate Brush/Editor/` 路径下，创建一个 `CoordinateBrush.cs` 脚本，并且将下列代码粘贴复制到脚本中，然后等待 Unity 引擎编译结束，就能在 `Tile Palette` 面板中，找到该笔刷。

```c#
using UnityEngine;
using UnityEditor.Tilemaps;

namespace UnityEditor
{
    [CustomGridBrush(true, false, false, "Coordinate Brush")]
    [CreateAssetMenu(fileName = "New Coordinate Brush", menuName = "Brushes/Coordinate Brush")]
    public class CoordinateBrush : GridBrush
    {
        public int z = 0;


        public override void Paint(GridLayout grid, GameObject brushTarget, Vector3Int position)
        {
            var zPosition = new Vector3Int(position.x, position.y, z);
            base.Paint(grid, brushTarget, zPosition);
        }

        public override void Erase(GridLayout grid, GameObject brushTarget, Vector3Int position)
        {
            var zPosition = new Vector3Int(position.x, position.y, z);
            base.Erase(grid, brushTarget, zPosition);
        }

        public override void FloodFill(GridLayout grid, GameObject brushTarget, Vector3Int position)
        {
            var zPosition = new Vector3Int(position.x, position.y, z);
            base.FloodFill(grid, brushTarget, zPosition);
        }

        public override void BoxFill(GridLayout gridLayout, GameObject brushTarget, BoundsInt position)
        {
            var zPosition = new Vector3Int(position.x, position.y, z);
            position.position = zPosition;
            base.BoxFill(gridLayout, brushTarget, position);
        }

    }

    [CustomEditor(typeof(CoordinateBrush))]
    public class CoordinateBrushEditor : GridBrushEditor
    {
        private CoordinateBrush coordinateBrush { get { return target as CoordinateBrush; } }

        public override void PaintPreview(GridLayout grid, GameObject brushTarget, Vector3Int position)
        {
            var zPosition = new Vector3Int(position.x, position.y, coordinateBrush.z);
            base.PaintPreview(grid, brushTarget, zPosition);
        }

        public override void OnPaintSceneGUI(GridLayout grid, GameObject brushTarget, BoundsInt position, GridBrushBase.Tool tool, bool executing)
        {
            base.OnPaintSceneGUI(grid, brushTarget, position, tool, executing);
            if (coordinateBrush.z != 0)
            {
                var zPosition = new Vector3Int(position.min.x, position.min.y, coordinateBrush.z);
                BoundsInt newPosition = new BoundsInt(zPosition, position.size);
                Vector3[] cellLocals = new Vector3[]
                {
                    grid.CellToLocal(new Vector3Int(newPosition.min.x, newPosition.min.y, newPosition.min.z)),
                    grid.CellToLocal(new Vector3Int(newPosition.max.x, newPosition.min.y, newPosition.min.z)),
                    grid.CellToLocal(new Vector3Int(newPosition.max.x, newPosition.max.y, newPosition.min.z)),
                    grid.CellToLocal(new Vector3Int(newPosition.min.x, newPosition.max.y, newPosition.min.z))
                };

                Handles.color = Color.blue;

                int i = 0;
                for (int j = cellLocals.Length - 1; i < cellLocals.Length; j = i++)
                {
                    Handles.DrawLine(cellLocals[j], cellLocals[i]);
                }
            }

            var labelText = "Pos: " + new Vector3Int(position.x, position.y, coordinateBrush.z);
            if (position.size.x > 1 || position.size.y > 1)
            {
                labelText += " Size: " + new Vector2Int(position.size.x, position.size.y);
            }

            GUIStyle myStyle = new GUIStyle();
            myStyle.normal.textColor = Color.white;


            Handles.Label(grid.CellToWorld(new Vector3Int(position.x, position.y, coordinateBrush.z)), labelText, myStyle);
        }
    }
}
```

![自定义笔刷-显示坐标](https://imageshack.yuilexi.cn/Unity3D/Unity引擎/坐标系系/Unity坐标系/自定义笔刷-显示坐标.png)

![Scene中显示瓦片的坐标](https://imageshack.yuilexi.cn/Unity3D/Unity引擎/坐标系系/Unity坐标系/Scene中显示瓦片的坐标.png)

---

<div align="center" color="red" font-weight="blod">坐标的计算与转换</div>

以正方形的瓦片为例，单元格的大小为 $M\cdot M$ ，那么世界坐标的转换方式为：`（x,y,z）➗ M ，然后向下取整` 。

# 六、坐标系的转换

第一个启用的相机组件，标记为“主相机”（只读）。如果没有启用带有“主相机”标记的相机组件，则此属性为 null。

在内部，Unity 使用“主摄像机”标签缓存所有游戏对象。访问此属性时，Unity 会从其缓存中返回第一个有效结果。访问此属性的 CPU 开销很小，与调用 [GameObject.GetComponent](https://docs.unity3d.com/ScriptReference/GameObject.GetComponent.html) 相当。

屏幕坐标转世界坐标：

```c#
public Vector3 ScreenToWorldPoint(Vector3 position);
//屏幕坐标为三元数，z = 0
Vector3 Camera.ScreenToWorldPoint(new Vector3(screenPos.x , screenPos.y , zInfo));
Vector3 Camera.main.ScreenToWorldPoint(new Vector3(screenPos.x , screenPos.y , zInfo));
```

世界坐标转屏幕坐标：

```c#
public Vector3 WorldToScreenPoint(Vector3 position);
//世界坐标转屏幕坐标后，z = 0
Vector3 Camera.WorldToScreenPoint(new Vector3(worldPos.x , worldPos.y , worldPos.z));
Vector3 Camera.main.WorldToScreenPoint(new Vector3(worldPos.x , worldPos.y , worldPos.z));
```

世界坐标转视口坐标

```c#
public Vector3 WorldToViewportPoint(Vector3 position);
Vector3 Camera.WorldToViewportPoint(Vector3 position);
Vector3 Camera.main.WorldToViewportPoint(Vector3 position);
```

视口坐标转世界坐标

```c#
public Vector3 ViewportToWorldPoint(Vector3 position);
Vector3 Camera.ViewportToWorldPoint(new Vector3(viewPortPos.x , viewPortPos.y , zInfo));
Vector3 Camera.main.ViewportToWorldPoint(new Vector3(viewPortPos.x , viewPortPos.y , zInfo));
```

屏幕坐标转视口坐标

```c#
public Vector3 ScreenToViewportPoint(Vector3 position);
Vector3 ScreenCoordinate = Camera.main.ScreenToViewportPoint(Vector3 Position);
```

视口坐标转屏幕坐标

```c#
public Vector3 ViewportToScreenPoint(Vector3 position);
Vector3 ScreenCoordinate = Camera.main.ViewportToScreenPoint(Vector3 Position);
```

区别：

在 Unity 中，`Camera.ScreenToWorldPoint`和`Camera.main.ScreenToWorldPoint`都是用于将屏幕坐标系中的点转换为世界坐标系中的点的方法。它们的区别在于：

- `Camera.ScreenToWorldPoint`是摄像机对象的实例方法，需要使用特定的摄像机对象来进行转换；
- `Camera.main.ScreenToWorldPoint`是静态方法，可以直接使用全局的主摄像机来进行转换。

因此，当需要将屏幕坐标系中的点转换为世界坐标系中的点时，可以根据不同的场景需求选择使用这两个方法。

如果场景中只有一个主摄像机，并且需要频繁地进行屏幕坐标系和世界坐标系之间的转换，可以考虑使用`Camera.main.ScreenToWorldPoint`方法，以避免频繁地获取和传递摄像机对象。

如果场景中有多个摄像机对象，并且需要对不同的摄像机进行不同的转换操作，或者需要对屏幕坐标系进行自定义的转换操作，可以使用`Camera.ScreenToWorldPoint`方法，并传递相应的摄像机对象或自定义的参数。

# 说明

## 更新日志

{% folding 更新日志 %}

{% timeline 更新日志,orange %}

<!-- timeline 2023-6-5 -->

1. 补充了第五部分：瓦片地图坐标系

<!-- endtimeline -->

<!-- timeline 2023-6-5 -->

1. 完善二、三、四、六部分

<!-- endtimeline -->

<!-- timeline 2023-5-28 -->

1. 创建文档

<!-- endtimeline -->

{% endtimeline %}

{% endfolding %}
