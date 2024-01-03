---
title: GitHub入门
date: 2023-5-30 00:00:00
update: 2023-7-24 00:00:00
tags: [版本控制, Github, Git, 团队协作]
categories:
  - [版本控制, Github]
description: 此文章主要讲解，GitHub入门，包括GitHub的使用、GitHub的团队协作等。
mathjax:
katex:
img_path: 版本控制/Github/
---

# 前言

此文章主要讲解 Github 的认识和使用，不包含**如何注册 Github 账号**。

# 总览

![Github仓库总览](https://imageshack.yuilexi.cn/版本控制/Github/Github仓库总览.png)

- 左上角为 Github 的用户名 + 仓库名
- 右上角
  - 眼睛图标+Watch 字样，点击这个按钮就可以 Watch 该仓库，今后该仓库的更新信息会显示在用户的公开活动中。
  - Star 旁边的数组表示给这个仓库添加 Star 的人数，这个数越高，代表该仓库越受关注。
  - Watch 与 Star 不同的地方在于，Watch 之后该仓库的相关信息会在您的个人 Notifications 中显示，让用户可以追踪仓库的内容，而 Star 更像是书签，让用户将来可以在 Star 标记的列表中找到该仓库。
  - 另外，Star 数还是 GitHub 上判断仓库热门程度的标志之一。
  - fork 就是被人 copy 的次数。
- 中间从左到右依次是：
  1.  Code：显示该仓库的文件列表，以及该仓库的各种操作
  2.  Issues：关于此项目的问题讨论处，遇到的问题可以在这里谈论
  3.  Pull Request（PR）：发起 pull request 给原仓库，让他看到你修改的内容(需要先 fork 一份原仓库，然后修复里面的内容，再 pull request 给原仓库)
  4.  GitHub Discussions：是一个围绕开源或内部项目为社区提供协作沟通的论坛。 不像 GitHub Issues，讨论用于需要透明和可访问的对话，但不需要在项目板上进行跟踪，并且与代码无关。 讨论使公共论坛中能够进行流畅、公开的对话。通过连接和提供更集中的区域来连接和查找信息，讨论为更多协作对话提供了空间。
  5.  GitHub Actions：在 GitHub Actions 的仓库中自动化、自定义和执行软件开发工作流程。 您可以发现、创建和共享操作以执行您喜欢的任何作业（包括 CI/CD），并将操作合并到完全自定义的工作流程中。
  6.  Projects：GitHub 新推出的项目管理工具 Projects。协助开发者在开发流程中整合项目管理，让开发者可以直接在 GitHub 程序代码储存库中管理工作流程，而 Projects 的介面就像看板系统，能够图像化开发流程，用户可以根据团队使用需求建立工作流程架构，如“开发中”、“已完成”、“尚未开始进行”等，且能通过拖拉的方式，来调整工作流程栏位的顺序
  7.  Wiki：我们可以用它来实现项目信息管理，为项目提供更加完善的文档
  8.  Insignts：
      1. Pulse：显示该仓库最近的活动信息，该仓库中软件是无人问津还是在热火朝天的开发之中，从这里可以一目了然。
      2. Graphs：以图表的形式显示该仓库的各项指标，让用户轻松了解该仓库的活动倾向。

# Code 页

![Github的Code页_1](https://imageshack.yuilexi.cn/版本控制/Github/Github的Code页_1.png)

![Github的Code页_2](https://imageshack.yuilexi.cn/版本控制/Github/Github的Code页_2.png)

# 说明

## 更新日志

{% folding 更新日志 %}

{% timeline 更新日志,orange %}

<!-- timeline 2023-5-31 -->

1. 更新了 Code 页相关内容

<!-- endtimeline -->

<!-- timeline 2023-5-30 -->

1. 创建文件

<!-- endtimeline -->

{% endtimeline %}

{% endfolding %}
