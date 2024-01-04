---
title: 第三章 向量(Vector)【线性代数】
date: 2023-7-12 00:00:03
categories: 大学学习
tags: [大学, 数学, 线性代数基础知识, 考研, 机器学习, 神经网络]
description: 此篇涵盖线性代数的基础知识点，包括向量、矩阵、行列式、特征值、特征向量、线性方程组、线性相关、线性无关、秩、线性变换、线性空间、内积、正交、正交矩阵、正交变换、对称矩阵、对称变换、二次型、正定矩阵、正定二次型、相似矩阵、相似变换、特征值等。
mathjax: true
katex: true
image_path:  学习/大学本科/线性代数/3_向量.md/
---

# 三、向量(Vector)【线性代数】



# 一、向量的定义

<a name="star" alt="none"> </a><a name="第三章向量" alt="none"> </a>**定义**： $n$ 个数 $a_{1},a_{2},\dots ,a_{n}$ 组成的有序数组，称为**向量**。其中， $a_{i}$ 称为分量。 $n$ 称为向量的**维数**。



**行向量**：形如 $\begin{bmatrix} a_{1} & a_{2} & \dots  &a_{n}\end{bmatrix}$ 的向量，称为**行向量**。

**列向量**：形如 $\displaystyle \begin{bmatrix} a_{1} \\ a_{2} \\ \dots  \\ a_{n}\end{bmatrix}$ 的向量，称为**列向量**。

> 在没有特殊说明的情况下，提到向量一律按照列向量的对待



**零向量**：向量的各分量都为 0 ，记作 $\mathbf{0} $ 。



# 二、向量的线性关系

**线性组合**：设向量组 $\beta ,\alpha _{1},\alpha _{2},\dots ,\alpha _{s}$ 都是 $n$ 维向量，若存在 $k_{1},k_{2},\cdots ,k_{s} $ ，使得
$$
\beta =k_{1}\alpha_{1}+k_{2}\alpha_{2}+\cdots+k_{s}\alpha_{s}
$$
称该向量组是一个线性组合，或者线性表示。

> - 零向量可以由任意向量组来表示。
> - 向量组中的任一向量，都以可有向量组其余的向量表示。
> - 任意向量可由 $\varepsilon_{1}  =\left [1,0,\cdots ,0  \right ] ^{T} ,\varepsilon_{1}  =\left [0,1,\cdots ,0  \right ] ^{T},\dots ,\varepsilon_{n}  =\left [0,0,\cdots ,1  \right ] ^{T}$ 表示。
> - 不管向量是行向量还是列向量，都**左乘**系数。



上述线性组合可以表示为：
$$
\begin{bmatrix} \alpha _{1}  &\alpha _{2}  & \dots  &\alpha _{s}\end{bmatrix}\begin{bmatrix}k_{1}  \\k_{2} \\ \vdots \\k_{s}\end{bmatrix} = \beta 
$$



**向量组的等价**：给定两给向量组 $\alpha _{1} ,\alpha _{2},\cdots ,\alpha _{m}$ 和向量组 $\beta_{1} ,\beta_{2},\cdots ,\beta _{m}$ ，并且都是同维的，如果 $\alpha$ 向量组中的每个向量，都可以用 $\beta$ 向量组线性表示（或者 $\beta$ 向量组中的每个向量，都可以用 $\alpha$ 向量组线性表示）那么称**这两个向量组是等价的**，记作 $\begin{bmatrix}\alpha _{1}  & \alpha _{2} &\cdots  &\alpha _{m}\end{bmatrix}\cong\begin{bmatrix}\beta _{1}  & \beta _{2} &\cdots  &\beta _{m}\end{bmatrix} $ 。



性质：

1. 反身性： $\begin{bmatrix}\alpha _{1}  & \alpha _{2} &\cdots  &\alpha _{m}\end{bmatrix}\cong\begin{bmatrix}\alpha _{1}  & \alpha _{2} &\cdots  &\alpha _{m}\end{bmatrix} $ ；
2. 对称性：如果 $\begin{bmatrix}\alpha _{1}  & \alpha _{2} &\cdots  &\alpha _{m}\end{bmatrix}\cong\begin{bmatrix}\beta _{1}  & \beta _{2} &\cdots  &\beta _{m}\end{bmatrix} $ ，那么 $\begin{bmatrix}\beta _{1}  & \beta _{2} &\cdots  &\beta _{m}\end{bmatrix}\cong\begin{bmatrix}\alpha _{1}  & \alpha _{2} &\cdots  &\alpha _{m}\end{bmatrix} $ 。
3. 传递性： 如果 $\begin{bmatrix}\alpha \end{bmatrix}\cong\begin{bmatrix} \beta\end{bmatrix} $ ，且 $\begin{bmatrix} \beta\end{bmatrix}\cong\begin{bmatrix}\gamma \end{bmatrix} $ ，那么 $\begin{bmatrix}\alpha \end{bmatrix}\cong\begin{bmatrix}\gamma \end{bmatrix} $ 。



**线性无关**：已知 $\alpha _{1},\alpha _{2},\dots ,\alpha _{n}$ 是 $n$ 个 $m$ 维向量，若存在一组不全为 0 的系数 $k_{1},k_{2},\cdots ,k_{n} $ 使得
$$
k_{1}\alpha_{1}+k_{2}\alpha_{2}+\cdots+k_{n}\alpha_{n}=0
$$
成立，则称 $\alpha _{1},\alpha _{2},\dots ,\alpha _{n}$ 是**线性相关**的；反之，则称为**线性无关**。



> 1. 向量组中的两两向量成比例，则是线性相关的。
> 2. 含零向量的任意向量组必定是线性相关的。
> 3. 一个零向量必定线性相关的。
> 4. 任意一个非零向量必定线性无关。



**定理**：如果 $\alpha _{1} ,\alpha _{2},\cdots ,\alpha _{r}$ 是线性相关的，那么 $\alpha _{1} ,\alpha _{2},\cdots ,\alpha _{r},\alpha _{r+1},\cdots ,\alpha _{s}$ 必定也是线性相关的。



**定理** 1： $\alpha _{1} ,\alpha _{2} ,\cdots  ,\alpha _{s} $ 线性相关 $\Longleftrightarrow $ 至少一个向量可有其余向量线性表示。

**定理 2**： $\alpha _{1} ,\alpha _{2} ,\cdots  ,\alpha _{s} $ 线性无关，且 $\alpha _{1} ,\alpha _{2} ,\cdots  ,\alpha _{s}, \beta$ 线性相关，那么 $\beta $ 可由 $\alpha _{1},\alpha _{2},\cdots ,\alpha _{n}$ **唯一线性表示**。



>  $n+1$ 个 $n$ 维向量一定是线性相关的。（相当于 $n$ 个未知量和 $n+1$ 个方程）。



# 三、向量组的秩

**定义（极大线性无关组）**：向量组 $\alpha _{1},\alpha _{2},\cdots ,\alpha _{m}$ ， 而 $\alpha  _{1},\alpha  _{2},\cdots ,\alpha _{s} $ 是向量组的一部分，并且子向量组是线性无关的，同时任意的 $s+1$ 个向量是线性相关的，那么称 $\alpha  _{1},\alpha  _{2},\cdots ,\alpha _{s} $ 是一个**极大线性无关组**。



**定理**： $\alpha  _{1},\alpha  _{2},\cdots ,\alpha _{s} $ 线性无关，任意 $s+1$ 个向量是线性相关的。



**定义（向量组的秩）**：极大线性无关组所含向量的个数，即为**向量组的秩**。