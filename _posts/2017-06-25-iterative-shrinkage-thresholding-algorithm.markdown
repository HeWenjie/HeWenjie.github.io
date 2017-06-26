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

信号复原问题的基本模型：

$$Ax=b+w$$

已知$$A \in \mathbb{R}^{m \times n}$$和$$b \in \mathbb{R}^{m}$$，$$w$$是未知的噪声，$$x$$是未知的真实的信号/图像。

一个经典的解决上述问题的方法是最小二乘法:

$${\hat{x}}_{LS}=\underset{x}{argmin}\left\|Ax-b\right\|^{2}$$

当$$m=n$$以及A是非奇异矩阵（A是可逆的）时，最小二乘法的结果就是$${\hat{x}}_{LS}=A^{-1}b$$