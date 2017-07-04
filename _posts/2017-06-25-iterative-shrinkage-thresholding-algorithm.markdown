---
layout:     post
title:      "迭代收缩阈值算法"
subtitle:   "Iterative Shrinkage-Thresholding Algorithm"
date:       2017-06-23 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 论文笔记
---

> 本文涉及的论文：
> * 《A Fast Iterative Shrinkage-Thresholding Algorithm for Linear Inverse Problems》

### 线性逆问题

信号复原问题的基本模型：

$$Ax=b+w$$

已知$$A \in \mathbb{R}^{m \times n}$$和$$b \in \mathbb{R}^{m}$$，$$w$$是未知的噪声，$$x$$是未知的真实的信号/图像。

一个经典的解决上述问题的方法是最小二乘法:

$${\hat{x}}_{LS}=\underset{x}{argmin}\left\|Ax-b\right\|^{2}$$

当$$m=n$$以及A是非奇异矩阵（A是可逆的）时，最小二乘法的结果就是$${\hat{x}}_{LS}=A^{-1}b$$，但是A矩阵经常是病态的（ill-conditioned）

> 病态条件：解集$$x$$对$$A$$和$$b$$的系数高度敏感，当矩阵存在一个微小的误差，则会得到一个截然不同的解集$$x$$

为了解决病态条件，正则化方法被应用在问题中：

$${\hat{x}}_{TIK}=\underset{x}{argmin}\{\left\|Ax-b\right\|^{2}+\lambda\left\|Lx\right\|^{2}\}$$

$$L$$矩阵通常选择单位矩阵或者逼近一阶或二阶微分算子

另一种正则化的形式是：

$$\underset{x}{min}\{F(x)\equiv \left\|Ax-b\right\|^{2}+\lambda\left\|x\right\|_{1}\}$$

$$\left\|x\right\|_{1}$$是一阶范数：$$x$$中所有元素的绝对值之和

### 迭代收缩阈值算法ISTA

对于无约束最小化问题：

$$min\{f(x):x\in\mathbb{R}^{n}\}$$

其中$$f$$是$$\mathbb{R}^{n} \rightarrow \mathbb{R}$$的连续可微函数

解决上述无约束最小化问题的梯度方法是：

$$x_{0} \in \mathbb{R}^{n},x_{k}=x_{k-1}-t_{k}\triangledown f(x_{k-1})$$

其中$$t_{k} > 0$$是合适的步长。上述式子可以等价为：

$$x_{k}=\underset{x}{argmin}  \{ f( x_{k-1} +\left \langle x-x_{k-1},\triangledown f(x_{k-1}) \right \rangle + \frac{1}{2t_{k}} \left\| x-x_{k-1} \right\|^{2})\}$$

将上述的梯度思想应用到非光滑的$$l_{1}$$正则化问题上：

$$min\{f(x)+\lambda\left\|x\right\|_{1}:x\in\mathbb{R}^{n}\}$$

同样，上述式子等价为：

$$x_{k}=\underset{x}{argmin}\{f(x_{k-1}+\left\langle x-x_{k-1}, \triangledown f(x_{k-1})\right\rangle) + \frac{1}{2t_{k}}\left\|x-x_{k-1}\right\|^{2}+\lambda\left\|x\right\|_{1}\}$$

忽略常数项，上述式子可写为：

$$x_{k} = \underset{x}{argmin}\{\frac{1}{2t_{k}}\left\|x-(x_{k-1}-t_{k}\triangledown f(x_{k-1}))\right\|^{2}+\lambda\left\|x\right\|_{1}\}$$

由于$$l_{1}$$是可分的，$$x_{k}$$的计算可以变为解决每一个元素的一维最小化问题：

$$x_{k}=\tau_{\lambda t_{k}}(x_{k-1}-t_{k}\triangledown f(x_{k-1}))$$

$$\tau_{\alpha}$$是$$\mathbb{R}^{n} \rightarrow \mathbb{R}^{n}$$的函数:

$$\tau_{\alpha}(x)_{i}=(\left|x_{i}\right|-\alpha)+sgn(x_{i})$$

$$f(x):=\left\|Ax-b\right\|^{2}$$，为了保证$$x_{k}$$能收缩到一个极小值$$x^{*}$$，要求$$t_{k}\in (0,\frac{1}{\left\|A^{T}A\right\|})$$

上述问题更广义的模型

$$min\{F(x)\equiv f(x)+g(x):x\in \mathbb{R}^{n}\}$$

* $$g:\mathbb{R}^{n} \rightarrow \mathbb{R}$$是一个连续凸函数，但有可能不光滑

* $$f:\mathbb{R}^{n} \rightarrow \mathbb{R}$$是一个光滑的凸函数，如Lipschitz连续

  $$\left\|\triangle f(x)-\triangle f(y)\right\| \leqslant L(f) \left\|x-y\right\| \ for \ every \ x,y \ \in \mathbb{R}^{n}$$

  $$\left\| \cdot \right\|$$是欧几里得范数，$$L(f)>0$$是$$\triangledown f$$的Lipschitz常数

对于$$L > 0$$，$$F(x):=f(x)+g(x)$$在给定点$$y$$的二次近似是：

$$Q_{L}(x,y):=f(y)+\left \langle x-y, \triangledown f(y) \right \rangle+\frac{L}{2}\left\|x-y\right\|^{2}+g(x)$$

则极小值点是：

$$p_{L}(y):=argmin\{Q_{L}(x,y):x\in \mathbb{R}^{n}\}$$

忽略常数项：

$$p_{L}(y)=\underset{x}{argmin}\{g(x)+\frac{L}{2}\left\|x-(y-\frac{1}{L}\triangledown f(y))\right\|^{2}\}$$

ISTA的迭代步是：

$$x_{k}=p_{L}(x_{k-1})$$

$$L$$起到步长的作用

**固定步长ISTA算法：**

![固定步长ISTA算法](\img\ista\const-stepsize-ista-algorithm.png)

**固定步长ISTA算法过程：**

输入：$$L:=L(f)-\triangledown f$$的一个Lipschitz常数

1. 给定$$x_{0}\in \mathbb{R}^{n}$$

2. $$(k\geqslant 1)$$计算$$x_{k}=p_{L}(x_{k-1})$$

但是Lipschitz常数$$L(f)$$不容易求，考虑步长不固定的ISTA算法

**不固定步长ISTA算法：**

![不固定步长ISTA算法](\img\ista\backtracking-ista-algorithm.png)

**不固定步长ISTA算法过程：**

1. 给定$$L_{0}>0,\eta > 1,x_{0}\in \mathbb{R}^{n}$$

2. $$(k\geqslant 1)$$计算最小的非负整数$$i_{k}$$，有$$\bar{L}=\eta^{i_{k}}L_{k-1}$$，使得

   $$F(p_{\bar{L}}(x_{k-1})) \leqslant Q_{\bar{L}}(p_{\bar{L}}(x_{k-1}),x_{k-1})$$

   令$$L_{k}=\eta^{i_{k}}L_{k-1}$$并计算

   $$x_{k}=p_{L_{k}}(x_{k-1})$$ 

### 快速迭代收缩阈值算法FISTA

**固定步长FISTA算法：**

![固定步长FISTA算法](\img\ista\const-stepsize-fast-ista-algorithm.png)

**固定步长FISTA算法过程：**

输入：$$L:=L(f)-\triangledown f$$的一个Lipschitz常数

1. 给定$$y_{1}=x_{0} \in \mathbb{R}^{n}, t_{1}=1$$

2. $$(k\geqslant 1)$$计算

   $$x_{k}=p_{L}(y_{k})$$

   $$t_{k+1}=\frac{1+\sqrt{1+4t^{2}_{k}}}{2}$$

   $$y_{k+1}=x_{k}+\left ( \frac{t_{k}-1}{t_{k+1}}\right )(x_{k}-x_{k-1})$$

**不固定步长FISTA算法：**

![不固定步长FISTA算法](\img\ista\backtracking-fast-ista-algorithm.png)

**不固定步长FISTA算法过程：**

1. 给定$$L_{0}>0,\eta > 1, x_{0} \in \mathbb{R}^{n}$$，令$$y_{1}=x_{0},t_{1}=1$$

2. $$(k\geqslant 1)$$计算最小的非负整数$$i_{k}$$，有$$\bar{L}=\eta^{i_{k}}L_{k-1}$$，使得

   $$F(p_{\bar{L}}(y_{k})) \leqslant Q_{\bar{L}}(p_{\bar{L}}(y_{k}),y_{k})$$

   令$$L_{k}=\eta^{i_{k}}L_{k-1}$$并计算

   $$x_{k}=p_{L_{k}}(y_{k})$$

   $$t_{k+1}=\frac{1+\sqrt{1+4t^{2}_{k}}}{2}$$

   $$y_{k+1}=x_{k}+\left ( \frac{t_{k}-1}{t_{k+1}}\right )(x_{k}-x_{k-1})$$

FISTA与ISTA的不同在于下一个迭代点$$x_{k}$$不仅仅只依赖于前一个迭代点$$x_{k-1}$$，而是依赖于前两个迭代点$$\{x_{k-1},x_{k-2}\}$$的一个线性组合