---
layout:     post
title:      "卷积稀疏编码原理"
subtitle:   "Convolution Sparse Coding"
date:       2017-06-23 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 卷积稀疏编码
---

**《Classification of Histology Sections via Multispectral Convolution Sparse Coding》**

$$X=\{x_{i}\}^{N}_{i=1}$$是训练集，其中包含$$N$$张尺寸为$$m\times n$$的二维图片

$$D=\{d_{k}\}^{K}_{k=1}$$是二维滤波器组，其中包含$$K$$个$$h\times h$$的卷积核

$$Z=\{Z^{i}\}^{N}_{i=1}$$是稀疏特征图的集合，其子集$$Z^{i}=\{z^{i}_{k}\}^{K}_{k=1}$$包含K个尺寸为用于重构图片$$x_{i}$$的特征图，$$z^{i}_{k}$$的尺寸是$$(m+h-1) \times (n+h-1)$$

卷积稀疏编码旨在将每张训练图片$$x_{i}$$分解为一系列稀疏特征图$$z^{i}_{k}\in Z^{i}$$与滤波器组$$D$$中的核$$d_{k}$$卷积之和，通过解决如下目标函数：

![卷积稀疏编码目标函数](/img/convolution-sparse-coding/objective-function.png)

（$$\alpha$$是正则化参数，$$*$$是二维稀疏卷积操作）

关于$$D$$和$$Z$$的目标函数并不是联合凸函数，但是当其中一个变量（$$D$$或$$Z$$）固定时，对于另一个变量，目标函数就是凸函数。可以用凸优化的方法去优化目标函数。所以采用交替优化的方法去优化目标函数。本文中采用交替收缩阈值算法（ISTA）计算稀疏特征图$$Z$$，使用随机梯度下降来更新$$D$$的值

目标函数的优化过程如下所示：

![CSC算法](/img/convolution-sparse-coding/CSC-Algorithm.png)

CSC算法过程：

输入：训练集$$X=\{x_{i}\}^{N}_{i=1}$$，$$K$$，$$\alpha$$

输出：卷积滤波器组$$D=\{d_{k}\}^{K}_{k=1}$$

**（1）**初始化：将$$D$$随机初始化为服从均值为0、标准差为1的正态分布的滤波器组，将$$Z$$初始化为0

**（2）**对于训练集的每一张图片，执行（3）~（5）

**（3）**将$$D$$中的每一个卷积核归一化到0和1之间

**（4）**固定$$D$$，计算稀疏特征图$$Z^{i}$$，通过计算以下函数：

![固定D计算Z](/img/convolution-sparse-coding/Z.png)

**（5）**固定$$Z$$，通过以下方式更新$$D$$

![固定Z计算D](/img/convolution-sparse-coding/D.png)

**（6）**如果达到最大迭代次数或者目标函数$$\leq$$阈值，则算法结束；否则，返回（2）

---------------------------------------------------------------------------------

**《Fast Convolutional Sparse Coding》**

卷积稀疏编码的目标函数：

![卷积稀疏编码目标函数](/img/convolution-sparse-coding/csc-objective-function.png)

本文解决目标函数的方法是包含两个辅助变量$$t$$和$$s$$的引入以及目标函数在傅里叶域中的卷积部分：

![快速卷积稀疏编码目标函数](/img/convolution-sparse-coding/fcsc-objective-function.png)

本文使用增广拉格朗日乘子法来处理引入的等式约束：

![拉格朗日等式约束](/img/convolution-sparse-coding/augmented-lagrangian.png)

$$\lambda_{p}$$和$$\mu_{p}$$分别表示两个辅助变量（$$s$$和$$t$$）的拉格朗日乘子和惩罚项的权重，$$p\in \{s,t\}$$

* **子问题$$z$$**

  ![子问题z](/img/convolution-sparse-coding/subproblem-z.png)

  （z和d子问题的解决我还没看懂？？？？？？？？？）

* **子问题$$t$$**

  ![子问题t](/img/convolution-sparse-coding/subproblem-t.png)

  方程式（9）的$$t=[t_{1},...,t_{D}]^{T}$$的每个$$t_{p}$$（$$p\in [1,D]$$）可以分开计算

  ![子问题t](/img/convolution-sparse-coding/subproblem-t-1.png)

  对于每个$$t_{p}$$可以使用收缩函数计算（什么是收缩函数？？？？？）

  ![子问题t](/img/convolution-sparse-coding/subproblem-t-2.png)

* **子问题$$d$$**

  ![子问题d](/img/convolution-sparse-coding/subproblem-d.png)

* **子问题$$s$$**

  ![子问题s](/img/convolution-sparse-coding/subproblem-s.png)

  方程式（16）是QCQP问题，但是克罗内克积与单位矩阵可以被分解为K个独立的问题

  ![子问题s](/img/convolution-sparse-coding/subproblem-s-1.png)

  $$\Phi$$将最优解正交投影到无约束问题，因此$$s^{*}_{k}$$可以通过如下方式求得：

  ![子问题s](/img/convolution-sparse-coding/subproblem-s-2.png)

  方程式（19）可用以下公式求得：

  ![子问题s](/img/convolution-sparse-coding/subproblem-s-3.png)

  因此估计$$s_{k}$$的值不需要去构造子矩阵$$\Phi$$

* 拉格朗日乘子的更新

  ![拉格朗日乘子的更新](/img/convolution-sparse-coding/lagrange-multiplier-update.png)

* 惩罚项的更新

  如果$$\mu^{(i)} \rightarrow \infty$$，ADMM可能会实现超线性收敛