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



### 快速迭代收缩阈值算法FISTA