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

卷积稀疏编码旨在将每张训练图片$$x_{i}$$分解为一系列稀疏特征图$$z^{i}_{k}\in Z^{i}$$与滤波器组$$D$$中的核$$d_{k}$$卷积之和，目标函数：

![卷积稀疏编码目标函数](/img/convolution-sparse-coding/objective-function.png)

（$$\alpha$$是正则化参数，$$*$$是二维稀疏卷积操作）

关于$$D$$和$$Z$$的目标函数并不是联合凸函数，但是当其中一个变量（$$D$$或$$Z$$）固定时，对于另一个变量，目标函数就是凸函数。可以用凸优化的方法去优化目标函数。所以采用交替优化的方法去优化目标函数。本文中采用交替收缩阈值算法（ISTA）计算稀疏特征图$$Z$$，使用随机梯度下降来更新$$D$$的值

**CSC算法如下：**

![CSC算法](/img/convolution-sparse-coding/CSC-algorithm.png)

**CSC算法过程：**

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

  （z和d子问题的解法我还没看懂？？？？？？？？？）

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

  ![惩罚项的更新](/img/convolution-sparse-coding/penalty-update.png)

  本文注意到当$$\tau \in [1.01,1.5]$$以及$$\mu_{max}=1.0e^{5}$$会有较好的收敛结果

* 边界效应

  本文假设信号在边界上对称扩展，采用对称卷积来代替原来的循环卷积

**FCSC算法如下：**

![FCSC算法](/img/convolution-sparse-coding/FCSC-algorithm.png)

**FCSC算法过程：**

**（1）**初始化$$z^{(0)},t^{(0)},s^{(0)},\lambda^{(0)}_{s},\lambda^{(0)}_{t}$$

**（2）**使用快速傅里叶变换（FFT），$$z^{(0)},t^{(0)},s^{(0)},\lambda^{(0)}_{s},\lambda^{(0)}_{t} \rightarrow {\hat{z}}^{(0)},{\hat{t}}^{(0)},{\hat{s}}^{(0)},{\hat{\lambda}}^{(0)}_{s},{\hat{\lambda}}^{(0)}_{t}$$

**（3）**$$i=0$$

**（4）**对于给定的$${\hat{z}}^{(i)},{\hat{s}}^{(i)},{\hat{\lambda}}^{(i)}_{s}$$，求解$${\hat{d}}^{i+1}=F^{-1}\{({\hat{Z}}^{T}\hat{Z}+\mu_{s}I)^{-1}({\hat{Z}}^{T}\hat{x}+\mu_{s}\hat{s}-{\hat{\lambda}}_{s})\}$$

**（5）**使用逆快速傅里叶变换（inverse FFT），$$F^{-1} \{ {\hat{d}}^{(i+1)} \} \rightarrow d^{(i+1)}$$

**（6）**对于给定的$$d^{(i+1)}$$，求解$${\tilde{s}}^{(i+1)}=M\{\frac{1}{\mu_{s}}{\sqrt{D}}^{-1}(F^{-1} \{ {\hat{d}}_{k} \} + F^{-1} \{ {\hat{\lambda}}_{sk} \} ) \}$$

**（7）**

**（8）**对于所有的$$k=1...K$$，将$${\tilde{s}}^{(i+1)}$$投影到各向同性信任域约束来估计$$s^{(i+1)}_{k}$$

**（9）**对于给定的$${\hat{t}}^{(i)},{\hat{\lambda}}^{(i)}_{t},{\hat{d}}^{(i+1)}$$，求解$${\hat{z}}^{(i+1)}=F^{-1}\{({\hat{D}}^{T}\hat{D}+\mu_{t}I)^{-1}({\hat{D}}^{T}\hat{x}+\mu_{t}\hat{t}-{\hat{\lambda}}_{t})\}$$

**（10）**使用逆快速傅里叶变换（inverse FFT），$$F^{-1} \{ {\hat{z}}^{(i+1)} \} \rightarrow z^{(i+1)}$$

**（11）**对于给定$$z^(i+1),\lambda^{(i)}_{t}$$，求解$$t^{(i+1)}=arg \underset{t}{min} \frac{\mu_{t}}{2}\left\|z-t\right\|^{2}_{2}+\lambda^{T}_{t}(z-t)+\beta\left\|t\right\|_{1}$$

**（12）**更新拉格朗日乘子向量，$$\lambda^{(i+1)}_{t} \leftarrow \lambda^{(i)}_{t} + \mu_{t}(z^{(i+1)} - t^{(i+1)})$$

**（13）**更新拉格朗日乘子向量，$$\lambda^{(i+1)}_{s} \leftarrow \lambda^{(i)}_{s} + \mu_{s}(d^{(i+1)} - s^{(i+1)})$$

**（14）**使用快速傅里叶变换（FFT），$$t,s,\lambda_{s},\lambda_{t} \rightarrow \hat{t},\hat{s},{\hat{\lambda}}_{s},{\hat{\lambda}}_{t}$$

**（15）**$$i=i+1$$

**（16）**如果$$z,s,d,t$$收敛，则算法结束；否则，返回（4）