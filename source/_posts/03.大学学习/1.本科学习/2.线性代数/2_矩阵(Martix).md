---
title: 第二章 矩阵(Martix)【线性代数】
date: 2023-7-12 00:00:02
categories: 大学学习
tags: [大学, 数学, 线性代数基础知识, 考研, 机器学习, 神经网络]
description: 此篇涵盖线性代数的基础知识点，包括向量、矩阵、行列式、特征值、特征向量、线性方程组、线性相关、线性无关、秩、线性变换、线性空间、内积、正交、正交矩阵、正交变换、对称矩阵、对称变换、二次型、正定矩阵、正定二次型、相似矩阵、相似变换、特征值等。
mathjax: true
katex: true
image_path:  学习/大学本科/线性代数/2_矩阵(Martix).md/
---

#  二、矩阵(Matrix)【线性代数】

# 一、矩阵的概念

形如下面形式的一组**数表**，称为 $m\times n$ 矩阵：
$$
\begin{bmatrix} a_{11}  & a_{12} & \cdots  & a_{1n}\\ a_{21} & a_{22} & \cdots & a_{2n}\\ \vdots  & \vdots & \ddots  & \vdots\\ a_{m1} &a_{m2}  & \cdots &a_{mn}\end{bmatrix}
$$


矩阵与行列式区别：

- 矩阵是一组数表，而行列式本质上是一个数。
- 行列式符号是 $|\cdots|$ ；矩阵符号是 $(\dots) or [\dots]$ 。
- 行列式的**行权等于列权（地位相等）**，行列数必须相等，而矩阵行列数可以不相等。



**零矩阵**：元素全为 0 的**矩阵**称为零矩阵。

**单位矩阵**：对于一个方阵，主对角线上全为 1 ，其余全为 0 的矩阵，称为 **$n$ 阶单位矩阵**。
$$
\begin{bmatrix}1  & &  &\\ & 1 &  & \\  &  & \ddots  &\\&  &  &1\end{bmatrix}
$$
**同型矩阵**：行列数对应相等的两个矩阵，称为同型矩阵。

<div align="center">
    <font color="red">两个矩阵相等的一个前提是同型矩阵</font>
</div>


> - 只有一个数的矩阵，可以不用写符号。
> - 两个 0 矩阵不一定相等。



矩阵的属性：

1. 矩阵的秩
2. 特征值
3. 特征向量
4. 行列式



# 二、矩阵的运算

## 2.1 矩阵的加减法

两个矩阵能够相加减，必须是**同型矩阵**。矩阵的加减法是将每一个元素对应相加减。
$$
\begin{align} &\begin{bmatrix} a_{11} &  a_{12} & \cdots  & a_{1n} \\ a_{21} &  a_{22} & \cdots  & a_{2n} \\ \vdots  & \vdots  &  \ddots & \vdots\\ a_{m1} &  a_{m2} & \cdots  & a_{mn} \\\end{bmatrix} \pm \begin{bmatrix} b_{11} &  b_{12} & \cdots  & b_{1n} \\ b_{21} &  b_{22} & \cdots  & b_{2n} \\ \vdots  & \vdots  &  \ddots & \vdots\\ b_{m1} &  b_{m2} & \cdots  & b_{mn} \\\end{bmatrix}\\ & = \begin{bmatrix} a_{11}\pm b_{11} &  a_{12}\pm b_{12} & \cdots  & a_{1n}\pm b_{1n} \\ a_{21}\pm b_{21} &  a_{22}\pm b_{22} & \cdots  & a_{2n}\pm b_{2n} \\ \vdots  & \vdots  &  \ddots & \vdots\\ a_{m1}\pm b_{m1} &  a_{m2}\pm b_{m2} & \cdots  & a_{mn}\pm b_{mn} \\\end{bmatrix}\end{align}
$$
 运算律：

1.  $A\pm B=B\pm A$ ；
2.  $(A+B)+C=A+(B+C)$ ；
3.  $A+0=A$ ；



## 2.2 矩阵的数乘运算

一个数 $k$ 乘以一个矩阵，就是把矩阵中的每个元素都乘以 $k$ 。
$$
k\begin{bmatrix} a_{11} &  a_{12} & \cdots  & a_{1n} \\ a_{21} &  a_{22} & \cdots  & a_{2n} \\ \vdots  & \vdots  &  \ddots & \vdots\\ a_{m1} &  a_{m2} & \cdots  & a_{mn} \\\end{bmatrix}=\begin{bmatrix} ka_{11} &  ka_{12} & \cdots  & ka_{1n} \\ ka_{21} &  ka_{22} & \cdots  & ka_{2n} \\ \vdots  & \vdots  &  \ddots & \vdots\\ ka_{m1} &  ka_{m2} & \cdots  & ka_{mn} \\\end{bmatrix}
$$
矩阵提公因子：矩阵所有元素均有公因子，并且公因子**外提一次** 



## 2.3 矩阵的乘法

有两个矩阵 $A_{ml}$ 和矩阵 $B_{ln}$ ，矩阵 $A\times B$ 就是将 $A$ 的行元素对应乘以 $B$ 的列元素之和，就是新的矩阵的元素。（和行列式的乘法类似）。

> **矩阵相乘的前提是：左边的列数等于右边的行数**；矩阵相乘后的结果矩阵，其行数等于原左边矩阵的行数，其列数等于原右边矩阵的列数。




运算律：

1.  $AB\ne BA$ ，有时 $AB$ 有意义的时候， $BA$ 不一定有意义。
2.  $AB=0$ ，那么**推不出来** $A=0\text{或}B=0$ 。
3.  $AB=AC,A\ne0$ ，同样推不出来 $A=C$ 。
4.  $A0=0$ 。
5.  $AE=A,EB=B$ ，其中， $E$ 的单位矩阵。
6.  $(AB)C=A(BC)$ 。
7.  $(A+B)C=AC+BC,C(A+B)=CA+CB$ 。
8.  $kAB = k(AB) = A(kB)$ 。



## 2.4 矩阵的幂运算

形如 $A^{k} = AA\cdots A$ 的运算，称为**矩阵的幂运算**。特别地。规定 $A^{0}=E$ 。



运算律：

1.  $A^{k_{1}}A^{k_{2}}=A^{k_{1}+k_{2}}$ ；
2.  一般地， $(AB)^{k} \ne A^{k}B^{k}$ ；
3.  一般情况下， $(A+B)^{2} \ne A^{2}+2AB+B^{2} $ ；
    1.  $(A+B)^{2} = A^{2} +B^{2}+AB+BA $ ；
    2.  $(A-B)^{2} \ne A^{2}-2AB+B^{2}$；
    3.  $(A+E)^{2} = A^{2} +E^{2}+2AE $ ；



## 2.5 矩阵的转置

假设矩阵 $A=\begin{bmatrix} 1 & 2 & 3\\  1& 1 &1\end{bmatrix}$ ，那么，矩阵 $\begin{bmatrix} 1 & 1 \\  2& 1 \\ 3 & 1 \end{bmatrix}$ 称为 $A$ 的转置矩阵。

**定义（矩阵转置）** 设矩阵 $A_{mn}$ ，将对应的行换成列，对应的列换成行，得到的新矩阵称为 $A$ 的转置矩阵，记作 $A^{T}(\text{或者}A^{'})$ 。显然，转置矩阵是 $n\times m$ 阶的矩阵。



**性质**：

1. $(A^{T} )^{T} =A$ ；

2. $(A+B)^{T} =A^{T} +B^{T}$ ；

3. $(kA)^{T}=kA^{T}$ ；

4. $(AB)^{T}=B^{T}A^{T}$ ；

    ​    推广：

    1.  $(A_{1}A_{2}\cdots A_{n} )^{T}=A_{n} ^{T}\cdots A_{2} ^{T}A_{1} ^{T}$ ；



# 三、特殊矩阵 

## 3.1 数量矩阵

形如：
$$
\begin{bmatrix} a &  &  &  & \\  & a &  &  & \\  &  & a &  & \\  &  &  & \ddots  & \\  &  &  &  &a\end{bmatrix}
$$
其中， $a$ 是常数。这样的矩阵称为**数量矩阵**。**显然，数量矩阵都是方阵**。

> 单位阵和零矩阵都是特殊的单位矩阵。

**一个数乘以一个数量矩阵、两个数量矩阵之和、两个数量矩阵相减、两个数量矩阵相减、两个数量矩阵相乘，结果都是数量矩阵**。

## 3.2 对角矩阵

形如：
$$
\begin{bmatrix} a_{1}  &  &  &  & \\  & a_{2} &  &  & \\  &  & a_{3} &  & \\  &  &  & \ddots  & \\  &  &  &  &a_{n}\end{bmatrix}
$$
的矩阵称为**对角矩阵**。

> 数量矩阵是特殊的对角型矩阵。



## 3.4 三角矩阵

主对角线左下方全是 0 的矩阵是上三角矩阵；主对角线右上方全是 0 的矩阵是下三角矩阵。

> 对角型矩阵是特殊的上三角矩阵和下三角矩阵。



## 3.5 对称和反对称

**对称矩阵**：满足 $a_{ij}=a_{ji}$ 的矩阵称为对称矩阵。**$A^{T}=A$的矩阵称为对称矩阵**。 

1.  $A,B$ 是对称矩阵，那么 $A\pm B$ 也是对称矩阵。
2.  $A$ 是对称矩阵，那么 $(kA)^{T}$ 也是对称矩阵。
3.  $AA^{T},A^{T}A$ 都是对称矩阵。



**定义（反对称矩阵）** 满足 $a_{ij}=-a_{ji}$ 的矩阵，称为反对称矩阵。

> **反对称矩阵的主对角线全为零；对称矩阵的主对角线无要求**。



# 四、逆矩阵

之前，我已经探讨过矩阵的加、减、数乘、乘，那么，矩阵有没有除法呢？这就引出一个新的概念：**逆矩阵**。

## 4.1 方阵的行列式

设方阵 $A$ ，那么，将 $A$ 中所有元素构成一个行列式，这个行列式称为**方阵的行列式**，记作 $|A|$ 。

> 注意：行列式是一个数，而矩阵是一个数表。二者有着本质上的区别。**行列式是矩阵的一个属性**。



**性质**：

1. 性质1： $|A^{T}|=|A|$ ，即行列式转置，值不变。
2. 性质2： $|kA|=k^{n}|A|$ ，其中， $n$ 是方阵的阶。
3. 性质3： $|AB|=|A|\cdot|B|$ 。
    1. 性质3-1： $|A_{1}A_{2}\dots A_{n}|=|A_{1}||A_{2}|\cdots |A_{n}|$ 。



## 4.2 伴随矩阵

**定义（伴随矩阵）** 设矩阵 $\displaystyle \begin{bmatrix} a_{11} & a_{12} & \cdots  & a_{1n}\\ a_{21} & a_{22} & \cdots  & a_{2n}\\ \vdots  & \vdots  & \ddots  & \vdots \\ a_{m1} & a_{m2} & \cdots  & a_{mn}\end{bmatrix}$ ，并且 $A_{ij}$ 是矩阵 $A$ 的**代数余子式** ，然后代数余子式按顺序组成的矩阵的**转置矩阵**，称为矩阵 $A$ 的伴随矩阵，记作 $A^{*}$：
$$
\displaystyle A ^{\ast }=\begin{bmatrix} A_{11} & A_{21} & \cdots  & A_{m1}\\ A_{12} & A_{22} & \cdots  & A_{m2}\\ \vdots  & \vdots  & \ddots  & \vdots \\ A_{1n} & A_{2n} & \cdots  & A_{nm}\end{bmatrix} =\begin{bmatrix} A_{11} & A_{12} & \cdots  & A_{1n}\\ A_{21} & A_{22} & \cdots  & A_{2n}\\ \vdots  & \vdots  & \ddots  & \vdots \\ A_{m1} & A_{m2} & \cdots  & A_{mn}\end{bmatrix}^{T}
$$

<div align="center">
    <p>
        --------------------
    </p>
    <p>
        按行求，按列放
    </p>
    <p>
        --------------------
    </p>
</div>

**定理1**：任意方阵 $A$ ，那么 $AA^{\ast }=A^{\ast }A=|A|E$ 。

证明：
$$
\begin{align}AA^{\ast } & = \begin{bmatrix} a_{11} & a_{12} & \dots  & a_{1n}\\a_{21} & a_{22} & \dots & a_{2n}\\ \vdots  &  \vdots&  \ddots & \vdots\\ a_{m1} & a_{m2} & \dots &a_{mn}\end{bmatrix}\begin{bmatrix} A_{11} & A_{21} & \cdots  & A_{m1}\\ A_{12} & A_{22} & \cdots  & A_{m2}\\ \vdots  & \vdots  & \ddots  & \vdots \\ A_{1n} & A_{2n} & \cdots  & A_{nm}\end{bmatrix}\\&=\begin{bmatrix} |A| &  &  & \\  & |A| &  & \\  &  & |A| & \\  &  &  &|A|\end{bmatrix}=|A|E\end{align}
$$


**推论1-1**：当 $|A|\ne 0$ 时， $|A^{\ast}|=|A|^{n-1}$ 。（后续能够证明，当 $|A| =0$ ，该推论依旧成立）。

证明：
$$
\left | AA^{\ast} \right |  = \left | \left | A \right | E \right | =\left | A \right |^{n}  
$$
那么，上式左边可以化为：
$$
\left | A \right |^{n}  =\left | AA^{\ast} \right | =|A|\cdot |A^{\ast } |
$$
移项化简可得：
$$
|A^{\ast } |=\left | A \right |^{n-1} 
$$
至此证毕。



## 4.3 逆矩阵

**定义（逆矩阵）** 设矩阵 $A$ 为 $n$ 阶方阵，存在 $n$ 阶方阵 $B$ ，使得 $AB=BA=E$ ，那么，称矩阵 $B$ 为 $A$ 的逆矩阵，记作： $A^{-1}$ 。

> 注意：不要写成 $\displaystyle \frac{1}{A} $ 样的形式，这样的表示是错误的。 $A^{-1}$ 表示的是逆矩阵，而不是矩阵 $A$ 的负一次方。

结论：

1. 未必所有方阵均可逆；
2. 如果矩阵 $A$ 可逆，那么逆矩阵一定唯一。

## 4.4 逆矩阵的判定

奇异矩阵和非奇异矩阵：

- 如果矩阵 $A$ 满足 $|A|\ne 0$ ，那么矩阵 $A$ 称为非奇异矩阵、非退化矩阵、满秩矩阵；
- 如果矩阵 $A$ 满足 $|A|= 0$ ，那么矩阵 $A$ 称为奇异矩阵、退化矩阵、非满秩矩阵。



**定理**：矩阵 $A$ 可逆的**充分必要条件**是： $|A|\ne 0$ ，此时， $\displaystyle A^{-1}=\frac{1}{|A|} A^{\ast }$ 。



**推论**：设 $A,B$ 都为 $n$ 阶方阵，如果 $AB=E\quad(BA=E)$ ，那么 $A$ 可逆，且 $A^{-1}=B$ 。♨️♨️♨️♨️♨️♨️



## 4.5 逆矩阵的求法

方法

1. 方法一：伴随矩阵法。

    ​    （上面的内容已经证明过了）

2. 方法二：初等变换法。



## 4.5 逆矩阵的性质

**性质 4**：如果矩阵 $A$ 可逆，那么 $A^{-1}$ 也可逆，并且 $(A^{-1} )^{-1} $ 。



**性质 5**：如果矩阵 $A,B$ 均可逆，那么 $AB$ 可逆，并且 $(AB)^{-1}=B^{-1} A ^{-1} $ 。



**性质 6**：如果矩阵 $A$ 可逆，那么 $A^{T} $ 可逆，并且 $(A^{T} )^{-1}=(A^{-1} )^{T} $ 。



**性质 7**：如果矩阵 $A$ 可逆，那么 $\displaystyle |A^{-1}| = |A|^{-1} = \frac{1}{|A|} $ 。



**性质 8**：如果矩阵 $A$ 可逆，那么 $A^{\ast } $ 也可逆，并且 $\displaystyle (A^{\ast } )^{-1} = \frac{1}{|A|}A$ 。



## 2.5 分块矩阵

将一个高阶的矩阵进行分块（注意：分块的时候要按整行或者整列进行切割）
$$
\begin{bmatrix} 1 & 2 & 3 \vdots& 4\\\dots &\dots &\dots \vdots&\dots \\ 5 &  6& 7 \vdots&8 \\ 9 &  10& 11 \vdots& 12\\ 13 &  14&  15\vdots&16\end{bmatrix}=\begin{bmatrix} A_{11} & A_{12}\\ A_{21} &A_{22}\end{bmatrix}
$$


**标准形**： 
$$
\begin{bmatrix} 1 &  &  &  &  & \\  &  \ddots &  &  &  & \\  &  & 1 &  &  & \\  &  &  & 0 &  & \\  &  &  &  & \ddots  & \\  &  &  &  &  &0\end{bmatrix}_{m\times n} =\begin{bmatrix} E & 0\\ 0 &0\end{bmatrix}
$$


分块矩阵的运算与一般矩阵的运算基本相同。



分块矩阵的转置：
$$
\begin{bmatrix} A_{11} & A_{12}\\ A_{21} &A_{22}\end{bmatrix}^{T}= \begin{bmatrix} A_{11}^{T} & A_{21}^{T}\\ A_{12}^{T} &A_{22}^{T}\end{bmatrix}
$$

---

例题：设分块矩阵 $\displaystyle H=\begin{bmatrix} A_{m\times m}  & C_{m\times n}\\  0&B_{n\times n}\end{bmatrix}$ ， $A,B$ 是可逆矩阵，求证： $|H|=|A|\cdot |B|$ 。

证明：使用拉普拉斯定理得：
$$
\begin{bmatrix} A_{m\times m} & C_{m\times n}\\  0&B_{n\times n}\end{bmatrix}=A\cdot (-1)^{m+m}\cdot B=AB
$$
因此，可以得到：
$$
|H|=|A|\cdot|B|
$$

# 六、初等变换和初等矩阵

## 6.1 初等变换

初等行变换和初等列变换：

1. 交换矩阵得某两行（列）元素。
2. 某一行（列）乘以 $k(k\ne 0)$ 。
3. 某一行（列）的 $k$ 倍加到另一行（列）上去。

$$
[\dots ]\to [\dots ]\to [\dots ]
$$

> 注意：初等变换后的矩阵与原矩阵不一定相等，这是一种等效变换，但是不是相等变换。
>
> 注意：**初等变换是一种可逆变换**。



**定理**：任何矩阵可以通过**初等变换**化为标准形。



## 6.2 等价

**定义**：矩阵 $A$ 经过初等变换得到矩阵 $B$ ，那么称 $A,B$ 是等价的，记作 $A\cong B $ 。



**性质**：

1. **反身性**： $A\cong A$ ；
2. **对称性**：已知 $A\cong B $ 。那么 $B\cong A$ ；
3. **传递性**：已知 $A\cong B,B\cong C$ ，那么 $A\cong C$ 。



## 6.3 初等方阵

**定义**：对一个 $n$ 阶的单位矩阵做一次初等变换，得到的矩阵称为**初等方阵**。

> 初等方阵均可逆。其逆矩阵也是初等方阵。初等方阵的转置矩阵也是初等矩阵。



**定理**：设 $A$ 是任意一个矩阵，并且存在一个进行第 $i$ 种初等变换得到的初等矩阵，初等矩阵**左乘**矩阵 $A$ ，相当于对矩阵 $A$ 实施了同种的第 $i$ 种初等行变换。如果是初等矩阵**右乘**矩阵 $A$ ，相当于对 $A$ 实施了同种的第 $i$ 种初等行变换。



**初等变换的作用**：——左行右列——

`E(...)A = B` ，注意，**这里是 `=` 号。我们在之前的学习中知道，初等变换后的矩阵与原矩阵，一般不相等，因此用 `→` 表示，但是，有时为了方便计算和证明，我们需要进行 `=` 的使用，那么，这时候就可以用左乘右乘，来建立一种相等的关系**。



**定义 3**：对于任意的矩阵 $A$ ，存在初等矩阵 $P_{1},P_{2},\dots ,P_{s}$ 和初等矩阵 $Q_{1},Q_{2},\dots ,Q_{t}$ ，使得 $P_{1}P_{2}\dots P_{s}AQ_{1}Q_{2}\dots Q_{t}$ 为标准形。

- 解释：矩阵 $A$ 通过初等变换化为标准形，那么，在初等变换的过程中，可能有行变换（左乘），可能有列变换（右乘），那就相当于左乘和右乘若干矩阵。



**推论 1**：已知矩阵 $A,B$ 是等价的，那么存在可逆矩阵 $P,Q$ ，使得 $PAQ=B$ 。

- 解释：因为 $A,B$ 是等价的，就是说 $A$ 可以通过若干次初等变换，得到矩阵 $B$ ，即**相当于左乘和右乘对应的初等矩阵**。此外，初等矩阵是可逆矩阵，那么初等矩阵的乘积也是可逆矩阵，将所有左乘的初等矩阵相乘，得到 $P$ ，右乘矩阵 $Q$ 同理，最终能得到 $PAQ=B$ 。



**定义 4**：已知矩阵 $A$ 是可逆矩阵，那么 $A$ 的标准形为单位矩阵 $E$ 。（ $\Leftrightarrow $ ）

- 必要性：
- 充分性：



**定义 5**：矩阵 $A$ 是可逆矩阵 $\Leftrightarrow $  $A=P_{1}P_{2}\cdots P_{s}$ 。



## 6.4 初等变换法求逆阵

设矩阵 $A$ 可逆，那么 $A^{-1}$ 也是可逆的。所以，可以把 $A^{-1}$ 表示成：
$$
A^{-1}=Q_{1}Q_{2}\dots Q_{t}
$$
上式同时右乘矩阵 $A$ 得：
$$
A^{-1}A=Q_{1}Q_{2}\dots Q_{t}A=E
$$
而根据前面的学习，我们知道：$Q_{1}Q_{2}\dots Q_{t}E=A^{-1}$ 。

最终得到如下两个式子：
$$
\begin{align}& Q_{1}Q_{2}\dots Q_{t}A=E  &(1)\\& Q_{1}Q_{2}\dots Q_{t}E=A^{-1} &(1)\\\end{align}
$$
式子 (1) 相当于将 $A$ 进行 $t$ 得初等行变换，最终化为单位矩阵 $E$ ，而单位矩阵 $E$ 进行**一样**的初等行变换，那么最终会化为 $A^{-1}$ 。

上述求解**逆矩阵**的方法，被称为**初等行变换法**，构造为 $\begin{bmatrix} A & \vdots  &E\end{bmatrix}$ ，经过初等行变换得到 $\begin{bmatrix} E & \vdots  &A^{-1} \end{bmatrix}$ 。（只做行变换，不能做列变换）

> 同样的，还存在**初等列变换**求解逆矩阵，与行变换类似。构造的形式是 $\begin{bmatrix}A \\\dots  \\E\end{bmatrix}\to \begin{bmatrix}E \\\dots  \\A^{-1} \end{bmatrix}$ 。



# 七、矩阵的秩

## 7.1 秩的定义

**定义（ $k$ 阶子式）**：对于给定的矩阵 $A$ ，任取 $k$ 行 $k$ 列，**组成的行列式**，称为 $k$ 阶子式。

非零子式的最高阶数，就称为**矩阵的秩**，记作 $R(A)$ 。

---

那么，矩阵的秩有什么含义吗？

<font color='red'>行阶梯矩阵</font> 是指满足下面两个条件的矩阵：

1. 如果有零行（元素全为零的行），则零行位于非零行的下方；
2. 非零行的首个非零元素（亦称为基准或主元素），前面零元的个数从上往下依次严格增加。

当行阶梯矩阵进一步满足：非零行的首非零元均为 1 ，且所在列的其余元素均为 0 ，则称为 <font color='red'>最简行阶梯矩阵</font>（或称<font color='red'>行最简形</font>） 。

**矩阵的秩就是表示行阶梯矩阵非零行的个数**。

**满秩**：行阶梯矩阵没有非零行，即非零子式的最高阶数等于矩阵的阶数（行数、列数中的较小值）。



## 7.2 秩的性质

**性质 1**：已知矩阵 $A$ 是 $m\times n$ 矩阵，那么 $0\le R(A)\le \mathrm{min} \left \{ m,n \right \} $ 。

- 行满秩： $R(A)=m$ 取所有行，行满秩；
- 列满秩： $R(A)=n$ 取所有列，列满秩；
- 行满秩与列满秩，都是满秩。
- 如果 $R(A)< \mathrm{min} \left \{ m,n \right \} $ ，那么就称为**降秩**。
- $A$ 是方阵，且 $A$ 是满秩 $\Leftrightarrow $ $A$ 是可逆矩阵  $\Leftrightarrow $  $|A|\ne 0$ 。



**定理 1**： $R(A)=r$  $\Leftrightarrow $ 有一个 $r$ 阶子式不为 0 ，所有的 $r+1$ 阶子式全为 0 .



**性质 2**： $R(A)=R(A^{T})$ 。



**性质 3**：$R(A)=0\iff A=O$ 。



**性质 4**：若 $A\cong B$ ，则 $\displaystyle R(A) = R(B)$ 。



**性质 5**：$\displaystyle R\begin{bmatrix}A & O\\O & B\end{bmatrix} = R(A) +R(B)$ 。



**性质 6**：$\displaystyle R(A\pm B)\le R(A)+R(B)$ 。

- 证明：
    $$
    \begin{align}\displaystyle \begin{bmatrix} A & O\\ O &B\end{bmatrix}\cong  \displaystyle \begin{bmatrix} A & O\\ A &B\end{bmatrix}\cong  \displaystyle \begin{bmatrix} A & A\\ A &A+B\end{bmatrix}\end{align}
    $$
    ​    所以：
    $$
    \displaystyle R(A)+R(B) = R\begin{bmatrix} A & A\\ A &A+B\end{bmatrix}\ge R(A+B)
    $$
    ​    同理可证：
    $$
    \displaystyle R(A)+R(B)\ge R(A- B)
    $$



**性质 7**：${\color{Red} \displaystyle \text{max}\left \{ R(A),R(B) \right \} \le R(A,B)\le R(A)+R(B)} $ 。



**性质 8**：若 $A$ 为 $n$ 阶方阵，则 $\displaystyle R(A)=n\Longleftrightarrow A $ 是可逆矩阵。



**性质 9**：$\displaystyle R(AB)\le\text{min} \left \{ R(A),R(B) \right \} $ （**乘法矩阵的秩在减小，只有都是满秩才取等**）。



**性质 10**：若 $P,Q$ 可逆，则 $\displaystyle R(A) = R(PA)=R(AQ)=R(PAQ)$ （可逆矩阵不影响矩阵的秩，可逆矩阵一定是满秩）；



**性质 11**：若 $A,B$ 均为 $n$ 阶方阵，则 $\displaystyle R(AB)\ge R(A)+R(B)-n$ 。

 （ $\displaystyle R(A)+R(B)-n\le R(AB)\le \text{min}\left \{ R(A),R(B) \right \} $ ，上式被称为<font color='red'>西尔维斯特不等式</font>）。



**性质 12**：**`Frobenius`**不等式： $\displaystyle R(ABC)\ge R(AB)+R(BC)-R(B)$ ；



**性质 13**：$\displaystyle R(A_{m\times n} )=n\Longleftrightarrow \text{齐次方程组}  Ax=0 \text{只有零解}$ 。