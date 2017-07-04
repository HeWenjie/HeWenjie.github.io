---
layout:     post
title:      "卷积稀疏编码原理"
subtitle:   "Convolutional Sparse Coding"
date:       2017-07-03 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 卷积稀疏编码
---

> 本文涉及的论文：
> * 《Fast Convolutional Sparse Coding With Separable Filters》

卷积稀疏编码广泛使用的公式：

$$\underset{\{u_{k}\}}{argmin} \frac{1}{2}\left\|\sum_{k=1}^{K}H_{k}*u_{k}-b\right\|^{2}_{2}+\lambda\sum_{k=1}^{K}p(u_{k})$$

$$\{H_{k}\}$$是K个不可分的$$L_{1}\times L_{2}$$滤波器的集合，$$\{u_{k}\}$$是对应的系数图集合，$$b$$是$$N_{1}\times N_{2}$$的输入图像，惩罚函数$$p(x)=\left\|x\right\|_{1}$$

任何由大量不可分离的滤波器组成2D滤波器组$$\{H_{k}\}$$，可近似为数量相对较少的可分离的滤波器$$\{G_{r}\}$$的线性组合

$$H_{k}\approx \sum_{r=1}^{R}\alpha_{kr}G_{r} \ \ \ \ k\in \{1,2,...,K\}$$

假设上述式子为等式，则有

$$\sum_{k=1}^{K}H_{k}*u_{k}=\sum_{r=1}^{R}G_{r}*\left ( \sum_{k=1}^{K}\alpha_{kr}u_{k}\right )$$

本文提出一个基于FISTA/NIHT的方法解决如下问题：

$$\underset{\{u_{k}\}}{argmin} \frac{1}{2}\left\|\sum_{r=1}^{R}G_{r}*\left ( \sum_{k=1}^{K}\alpha_{kr}u_{k}\right )-b\right\|^{2}_{2}+\lambda\sum_{k=1}^{K}p(u_{k})$$

$$p(x)=\alpha\left\|x\right\|_{1}+\beta\phi_{nng}(x)$$

$$$$