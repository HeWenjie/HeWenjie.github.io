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

### 卷积稀疏编码

卷积稀疏编码广泛使用的公式：

$$\underset{\{u_{k}\}}{argmin} \frac{1}{2}\left\|\sum_{k=1}^{K}H_{k}*u_{k}-b\right\|^{2}_{2}+\lambda\sum_{k=1}^{K}p(u_{k}) \ \ \ \ (1)$$

$$\{H_{k}\}$$是K个不可分的$$L_{1}\times L_{2}$$滤波器的集合，$$\{u_{k}\}$$是对应的系数图集合，$$b$$是$$N_{1}\times N_{2}$$的输入图像，惩罚函数$$p(x)=\left\|x\right\|_{1}$$

任何由大量不可分离的滤波器组成2D滤波器组$$\{H_{k}\}$$，可近似为数量相对较少的可分离的滤波器$$\{G_{r}\}$$的线性组合

$$H_{k}\approx \sum_{r=1}^{R}\alpha_{kr}G_{r} \ \ \ \ k\in \{1,2,...,K\}\ \ \ \ (2)$$

假设上述式子为等式，则有

$$\sum_{k=1}^{K}H_{k}*u_{k}=\sum_{r=1}^{R}G_{r}*\left ( \sum_{k=1}^{K}\alpha_{kr}u_{k}\right )\ \ \ \ (3)$$

本文提出一个基于FISTA/NIHT的方法解决如下问题：

$$\underset{\{u_{k}\}}{argmin} \frac{1}{2}\left\|\sum_{r=1}^{R}G_{r}*\left ( \sum_{k=1}^{K}\alpha_{kr}u_{k}\right )-b\right\|^{2}_{2}+\lambda\sum_{k=1}^{K}p(u_{k}) \ \ \ \ (4)$$

$$p(x)=\alpha\left\|x\right\|_{1}+\beta\phi_{nng}(x)\ \ \ \ (5)$$

这个算法也用来解决以下问题：

$$\underset{\{v_{k}\}}{argmin}\frac{1}{2}\left\|\sum_{r=1}^{R}G_{r}*v_{r}-b\right\|^{2}_{2}+\lambda\sum_{r=1}^{K}p(v_{r}) \ \ \ \ (6)$$

$$p(x)=\alpha\left\|x\right\|_{1}+\beta\phi_{nng}(x)$$

$$\{v_{r}\}$$可解释为可分离得系数图，而$$\{u_{k}\}$$为对应的不可分的系数图，它们的关系是：

$$v_{r}=\sum_{k=1}^{K}\alpha_{kr}u_{k} \ \ \ \ (7)$$


### ISTA及其变体

$$(1)$$和$$(4)$$可以找到这样一个矩阵$$H$$，使得：

$$Hu=\sum_{k=1}^{K}H_{k}*u_{k}=\sum_{r=1}^{R}G_{r}*v_{r}\ \ \ \ (8)$$

$$v_{r}=\sum_{k=1}^{K}\alpha_{kr}u_{k}$$以及$$u=[u_{1},u_{2},...,u_{K}]$$

则$$(1)$$和$$(4)$$可以写成：

$$\underset{\{u\}}{argmin}f(u)+\lambda \cdot p(u)\ \ \ \ (9)$$

上述式子中，$$\triangledown f(u)=H^{T}(Hu-b)$$，$$H^{T}$$可以由可分离的滤波器近似得到：

$$H^{T}x=[z_{1},z_{2},...,z_{R}]\cdot \alpha\ \ \ \ (10)$$

$$z_{r}=G_{r}\circ x,\alpha=[\alpha_{1},\alpha_{2},...,\alpha_{R}]^{T},\alpha_{r}=[\alpha_{1r},\alpha_{2r},...,\alpha_{Kr}]$$，$$\circ$$表示相关性（等价于与旋转了180度的卷积核进行卷积）

当$$p(u)=\left\|u\right\|_{1}$$时，在ISTA中对应的阈值规则是软阈值：

$$shrink(x,\lambda)=sign(x)max{0,|x|-\lambda}$$

当$$p(u)=\left\|u\right\|_{0}$$时，在ISTA中对应的阈值规则是硬阈值：

$$hard(x,\lambda)=I_{[|x|>\lambda]}\cdot x$$

其中$$I_{[cond]}$$是指示函数

##### ISTA

**ISTA算法如下：**

**输入：**$$\lambda$$（参数），$$u^{0}$$（估计初始值）

**步骤n：**$$(n \geqslant 1)$$计算

（1）$$\mu_{n}\in [0,\frac{1}{\left\|H_{T}H\right\|}]$$

（2）$$u^{n}=thresh(u^{n-1}+\mu_{n}H^{T}(b-Hx^{n-1}),t_{n}\lambda)$$

ISTA应用于$$(9)$$当$$f(u)=\frac{1}{2}\left\|Hu-b\right\|^{2}_{2}$$；$$thresh(\cdot)$$是对应于$$p(u)$$的阈值规则（例如当$$p(u)=\left\|u\right\|_{1}$$时，对应的阈值规则是软阈值，即$$thresh(x,\lambda)=shrink(x,\lambda)$$）

通常$$\mu_{n}=\frac{1}{\tau}$$，其中$$\tau=2\cdot \sigma_{max}(H^{T}H)$$是$$\triangledown f(u)$$的最小的Lipschitz常数，也可以通过每次迭代更新

##### FISTA

**FISTA算法如下：**

**输入：**$$\lambda$$（参数），$$L$$（$$\triangledown f(u)$$的Lipschitz常数）

**步骤0：**设$$y_{1}=u_{0}$$（估计初始值），$$\beta_{1}=1$$

**步骤n：**$$(n \geqslant 1)$$计算

（1）$$u^{n}=thresh(u^{n-1}+\frac{1}{L}H^{T}(b-Hy^{n}),\frac{\lambda}{L})$$

（2）$$\beta_{n+1}=\frac{1+\sqrt{1+4\beta_{n}}}{2}$$

（3）$$y^{n+1}=u^{n}+\frac{\beta_{n}-1}{\beta_{n+1}}(u^{n}-u^{n-1})$$

FISTA应用于$$(9)$$当$$f(u)=\frac{1}{2}\left\|Hu-b\right\|^{2}_{2}$$

##### NIHT

IHT/NIHT应用于$$(9)$$当$$p(u)=\left\|u\right\|_{0}$$，$$l_{0}$$范数时非零元素的数目，**NIHT算法如下：**

**输入：**$$\lambda$$

**步骤0：**$$u^{0}$$（估计初始值）

**步骤n：**$$(n \geqslant 1)$$计算

（1）$$v^{n}=H^{T}(b-Hu^{n}),\mu_{n}=\frac{(v^{n})^{T}v^{n}}{(v^{n})^{T}H^{T}Hv^{n}}$$

（2）$$u^{n}=hard(u^{n-1}+\mu_{n}v^{n},\mu_{n}\lambda)$$

IHT应用于$$(9)$$当$$f(u)=\frac{1}{2}\left\|Hu-b\right\|^{2}_{2}$$且$$p(u)=\left\|u\right\|_{0}$$

### 基于FISTA/NIHT算法

**输入：**$$\lambda$$

**步骤0：**设$$y^{(1)}=z^{(0)}$$（对$$(4)$$或$$(6)$$的估计初始值）

**步骤n：**$$(n \geqslant 1)$$计算

（1）$$c_{n}$$：计算$$z^{n-1}$$的$$l_{1}$$损失

（2）$$\varepsilon^{(n)}=H^{T}(b-Hy^{(n)}),v=H\varepsilon^{(n)},\mu_{n}=\frac{(\varepsilon^{(n)})^{T}\varepsilon^{(n)}}{v^{T}v}$$

（3）$$z^{(n)}=thresh(z^{(n-1)}+\frac{1}{\mu_{n}}\varepsilon^{(n)},\frac{\lambda}{\mu_{n}},\alpha,\beta)$$

（4）$$\beta_{(n+1)}=\frac{1+\sqrt{1+4\beta_{n}}}{2},y^{(n+1)}=z^{(n)}+\frac{\beta_{n}-1}{\beta_{n+1}}(z^{(n)}-z^{(n-1)})$$

$$thresh(\cdot)$$函数如下所示：

$$thresh(x,\lambda,\alpha,\beta)=\left\{\begin{matrix}\gamma(x,\lambda,\alpha,\beta)\ \ \ \ if \ |x|>\lambda \cdot (\alpha+\beta)\\0 \ \ \ \ otherwise\end{matrix}\right.$$

$$\gamma(x,\lambda,\alpha,\beta)=\frac{x(x-sign(x)\frac{2}{\lambda \cdot \alpha})+\lambda^{2}\cdot \alpha^{2}-\lambda^{2}\cdot \beta^{2}}{x+\lambda \cdot \alpha}$$

本文约束$$\alpha + \beta = 1$$，并且在每一次迭代中改变$$\alpha$$和$$\beta$$的值，$$\alpha$$初始值接近$$1$$，然后缓慢减小

$$(5)$$惩罚函数会影响算法$$4$$收敛性，但《On the convergence of the iterative shrinkage/
thresholding algorithm with a weakly convex penalty》中指出，只要惩罚函数$$p(\cdot)$$是$$\rho$$-weakly convex的，则$$(4)$$和$$(6)$$就是convex的

> **$$\rho$$-weakly convex**
> 
> 如果当$$\tau \geqslant \rho \geqslant 0$$时，$$\Theta(x)=\frac{\tau}{2}\left\|x\right\|^{2}_{2}+p(x)$$是convex的，则函数$$\Theta$$是$$\rho$$-weakly convex的

该文更是直接证明了当$$\rho=\frac{1}{2}$$时$$(5)$$惩罚函数是$$\rho$$-weakly convex的