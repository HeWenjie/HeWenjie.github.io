---
layout:     post
title:      "卷积稀疏编码原理"
subtitle:   "Convolutional Sparse Coding"
date:       2017-07-19 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 卷积稀疏编码
---

> 本文涉及的论文：
> * 《Convolutional Sparse Coding Classification Model For Image Classification》

### 概述

本文提出一种卷积稀疏编码分类方法，该方法结合卷积稀疏编码（CSC）和稀疏表示分类(SRC)方法。和传统CSC或字典训练不一样，该方法在训练阶段不是所有训练图像一起训练出滤波器，而是根据图像的类别，分别进行滤波器的学习；测试图像的标签，是由所有类别滤波器的重建残差来决定。

### 卷积稀疏编码分类系统

本文的CSCC模型结合了CSC框架和MFL方法（meta face learning）

##### CSCC模型

**数学符号**

* $$X_{i}=[x_{i,1},x_{i,2},...,x_{i,n_{i}}]$$表示第$$i$$类训练图像的集合

* $$x_{i,j}$$表示第$$i$$类图像的第$$j$$个样例，其维度是$$m \times m$$

* $$n_{i}$$是第$$i$$类训练图像的图像数量

* 图像数据集共$$N$$类，$$i=1,2,...,N$$

* $$F_{i}=[f_{i,1},f_{i,2},...,f_{i,C}]$$是第$$i$$类的卷积滤波器组

* $$f_{i,c}$$是第$$i$$类图像对应的滤波器组中第c个滤波器，其维度是$$s \times s$$

* 每一个滤波器组$$F_{i}$$都有$$C$$个滤波器$$(C < n_{i})$$

* $$Z_{i}=[Z_{i,1},Z_{i,2},...,Z_{i,j},...,Z_{i,n_{i}}]$$是稀疏特征图集合，其子集$$Z_{i,j}=[z^{(1)}_{i,j},z^{(2)}_{i,j},...,z^{(c)}_{i,j},...,z^{(C)}_{i,j}]$$包含$$C$$个用来重建图像$$x_{i,j}$$的特征图

CSC旨在将每一个训练图像$$x_{i,j}$$分解为一系列稀疏特征图$$z^{(c)}_{i,j}$$与滤波器组$$F_{i}$$中对应的核$$f_{i,c}$$卷积之和，为了使$$F_{i}$$具有第$$i$$类图像的标签信息，本文只使用第$$i$$类图像来训练滤波器组$$F_{i}$$，而不像传统的CSC一样使用所有的训练图像

$$F_{i}$$的训练可以写成以下的目标函数：

$$\underset{F_{i},Z_{i}}{argmin}\sum_{j=1}^{n_{i}}\left\{\left\|x_{i,j}-\sum_{c=1}^{C}f_{i,c}*z^{(c)}_{i,j}\right\|^{2}_{2}+\beta\sum_{c=1}^{C}\left\|z^{(c)}_{i,j}\right\|_{1}\right\}\ \ \ \ (1)$$

$$subject\ to\ \ \ \ \left\|f_{i,c}\right\|^{2}_{2}\leqslant 1\ \ \ \ \forall c \in \{1,2,...,C\}$$

$$\left\|x_{i,j}-\sum_{c=1}^{C}f_{i,c}*z^{(c)}_{i,j}\right\|^{2}_{2}$$表示重构残差，$$\beta\sum_{c=1}^{C}\left\|z^{(c)}_{i,j}\right\|_{1}$$表示$$l_{1}$$范数惩罚项，$$\beta$$是正则化参数，$$*$$表示2D卷积操作。

通过$$(1)$$训练的滤波器组$$F_{i}$$会有显著的重构图像$$X_{i}$$的能力，假设训练图像$$y$$是第$$i$$类的，通过$$F_{i}$$重构的效果应该比$$F_{m}(m \neq i)$$的重构效果要好，为了得到每一个滤波器组的重构表现，应该解决以下问题：

$$\underset{A}{argmin}\left\|y-\sum_{i=1}^{N}\sum_{c=1}^{C}f_{i,c}*\alpha_{i,c}\right\|^{2}_{2}+\lambda\sum_{i=1}^{N}\sum_{c=1}^{C}\left\|\alpha_{i,c}\right\|_{1}\ \ \ \ (2)$$

$$A=\{\alpha_{i,c}\}_{i=1,...,N;c=1,...,C}$$

$$\alpha_{i,c}$$是$$f_{i,c}$$对应的稀疏特征图，其维度是$$(m+s-1)\times (m+s-1)$$，$$\lambda$$是正则化参数。测试图像$$y$$的标签可以通过在所有类中的最小的残差决定，分类的重构残差表达式如下所示：

$$r_{i}=\left\|y-\sum_{c=1}^{C}f_{i,c}*\alpha_{i,c}\right\|^{2}_{2}\ \ \ \ (3)$$

$$i=1,2,...,N$$

**CSCC模型算法**如下所示：

![CSCC模型算法](/img/convolution-sparse-coding/CSCC-model.png)

**具体过程如下：**

**输入：**

* 测试图像$$y$$

* 正实数$$\lambda$$

* $$(1)$$中的卷积滤波器$$\{f_{i,c}\}(i=1,2,...,N;c=1,2,...,C)$$

**输出：**测试图像$$y$$的标签

**（1）**通过$$(2)$$计算稀疏特征图

**（2）**通过$$(3)$$计算重构残差$$r_{i}$$

**（3）**$$label(y)=argmin_{i}\{r_{i}\}$$

**（4）**返回$$y$$的标签

##### 优化策略

本文使用的方法是《Fast and flexible convolutional sparse coding》中的方法，为了有效地解决$$(1)$$，将$$(1)$$重新写成以下形式：

$$\underset{F_{i},Z_{i}}{argmin}\sum_{j=1}^{n_{i}}\{p_{1j}(\hat{F_{i}}\vec{Z_{i,j}})+p_{2j}(\vec{Z_{i,j}})\}+\sum_{c=1}^{C}p_{3}(\vec{f_{i,c}})\ \ \ \ (4)$$

$$\begin{cases} p_{1j}(v)=\left\|\vec{x_{i,j}}-Mv\right\|^{2}_{2}\\ p_{2j}(v)=\beta\left\|v\right\|_{1} \\ p_{3}(v)=ind_{J}(v)=\{0,v\in J;+\infty,v\notin J\} \end{cases}$$

$$J=\{v |\left\|Sv\right\|^{2}_{2}\leqslant 1\}$$

$$(4)$$可用**ADMM算法**求解，如以下算法所示：

![ADMM算法求解(4)](/img/convolution-sparse-coding/ADMM-algorithm.png)

**具体算法过程如下：**

**（1）**$$t=1$$

**（2）**$$y^{t+1}=argmin_{y}\left\|Ky-v^{t}+u^{t}\right\|^{2}_{2}$$

**（3）**$$v^{t+1}_{m}=prox_{\frac{p_{m}}{\rho}}(K_{m}y^{t+1}_{m}+u^{t}_{m})$$

**（4）**$$u^{t+1}=u^{t}+(Ky^{t+1}-v^{t+1})$$

**（5）**$$t=t+1$$，如果$$t\leqslant$$最大迭代次数，则返回（2）

算法2可以用来求解$$(1)$$中的$$F_{i}$$或$$Z_{i}$$当其中一个固定时，整个过程如下所示：

![(1)的优化过程](/img/convolution-sparse-coding/optimization-procedure.png)

**具体算法过程如下：**

**输入：**惩罚项参数$$\rho_{F}$$和$$\rho_{Z}$$

**初始化：**$$F^{0}_{i},Z^{0}_{i},u^{0}_{F},u^{0}_{Z},v^{0}_{F},v^{0}_{Z}$$

**（1）**$$t = 1$$

**（2）**滤波器更新：$$F^{t}_{i}\leftarrow argmin_{F_{i}}\sum_{j=1}^{n_{i}}p_{1j}(\hat{Z_{i,j}}\vec{F_{i}})+\sum_{c=1}^{C}p_{3}(\vec{f_{i,c}})$$，将参数$$\rho=\rho_{F},u=u^{t-1}_{F},v=v^{t-1}_{F}$$代入算法2

**（3）**稀疏特征图更新：$$Z^{t}_{i}\leftarrow argmin_{Z_{i}}\sum_{j=1}^{n_{i}}\left \{ p_{1j}(\hat{F_{i}}\vec{Z_{i,j}})+p_{2j}(\vec{Z_{i,j}})\right \}$$，将参数$$\rho=\rho_{Z},u=u^{t-1}_{Z},v=v^{t-1}_{Z}$$代入算法2

**（4）**$$t=t+1$$，如果$$t\leqslant$$最大迭代次数，则返回（2）

### 实验结果

**数据集**

* MNIST

* CIFAR-10

**与本文方法对比的方法**

* sparse representation classification(SRC)

* subspace SRC(S-SRC)

* meta face learning(MFL)

* collaborative representation classification(CRC)

* fisher discrimination dictionary learning(FDDL)

* label consistent k-svd(LCKSVD)

* CSC with SVM classifier(CSC+SVM)

所有的方法中的最优参数都是通过交叉校验来确定；两个数据集中的图像都是正方形；对于本文的模型，训练样本和测试样本都是原始的图像，而对于其他的稀疏表示和字典学习分类算法，所有图像需要被转化成列向量

##### MNIST手写数字分类

MNIST有$$10$$类手写数字数据集，包含$$60000$$张训练图像和$$10000$$张测试图像，所有的图像的维度都是$$28 \times 28$$

本文随机选取$$300$$张训练图片和$$1000$$张测试图片（每一类$$30$$张训练图片和$$100$$张测试图片），CSCC模型中参数的选择如下表所示：

![CSCC模型参数](/img/convolution-sparse-coding/CSCC-model-parameters.png)

每一种方法的识别率如下表所示：

![各方法实验结果](/img/convolution-sparse-coding/recognition-rate.png)

卷积核维度$$s$$的大小对实验结果的影响：

![不同维度对实验结果的影响](/img/convolution-sparse-coding/different-dimension.png)

如果$$s$$太小$$(s=5)$$，滤波器只能捕捉到对分类不重要的图像的边缘和局部特征；如果$$s$$太大$$(s=45)$$，滤波器中就会出现重叠图像，会给分类带来偏差，而且大维度会增加训练时间。

训练图片的数量对实验结果的影响：

![不同维度对实验结果的影响](/img/convolution-sparse-coding/different-training-size.png)

（训练集的大小分别是$$800,1600,2000,2500$$，训练集的大小都是$$1000$$，每一类滤波器的数量分别是$$20,40,50,60$$）

##### CIFAR-10目标识别

CIFAR-10数据集包含$$60000$$张$$32 \times 32$$彩色物体图片，一共$$10$$类，每类$$6000$$张图片，数据集被分为$$50000$$张训练图片和$$10000$$张测试图片

对于大多数稀疏表示和字典学习算法来说，CIFAR-10中原始彩色图片的维度太大了$$(32 \times 32 \times 3 = 3072)$$，所以所有的图像都转换为灰度图以获得相对较小维度的训练集$$(32 \times 32 = 1024)$$

本文随机选取$$800$$张训练图片和$$1000$$张测试图片（每一类$$80$$张训练图像和$$100$$张测试图像），参数的选择和实验结果见Table 1和Table 2