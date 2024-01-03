---
title: Visual Studio Code显示Unity依赖项
date: 2023-8-21 00:00:00
update: 2023-8-21 00:00:00
categories:
  - [Unity3D, 编辑器]
tags: [Unity3D, 编辑器, Visual Studio Code, 依赖项]
description: 此文章主要记录，如何在Visual Studio Code中显示Unity依赖项
image_path: Unity3D/编辑器/VisualStudioCode显示Unity依赖项/
---



使用**Visual Studio Code**来作为Unity的脚本编辑器时，可以极大的提升开发效率。但是，有时我们希望查看依赖库中的函数，经常定位不进去，报无法导航到插入点下面的符号。



这是因为，**VS Code**的工程代码没有把依赖库的代码导入进来，所以定位不到函数所在的位置。

解决办法：`Edit-> Preferences->External Tools`，把`Registry packages`勾选上，然后点击`Regenerate project files`。就会把我们从`Pakage manager`安装的代码都导入**VS Code**了。如果你的依赖库有从其他途径导入的，你要相应的勾选上选项。

<img src="https://imageshack.yuilexi.cn/Unity3D/%E7%BC%96%E8%BE%91%E5%99%A8/VisualStudioCode%E6%98%BE%E7%A4%BAUnity%E4%BE%9D%E8%B5%96%E9%A1%B9/%E4%BE%9D%E8%B5%96%E9%A1%B9%E8%AE%BE%E7%BD%AE.png" alt="依赖项设置" style="zoom:50%" />