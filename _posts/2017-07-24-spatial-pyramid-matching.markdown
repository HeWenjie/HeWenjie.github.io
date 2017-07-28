---
layout:     post
title:      "Spatial Pyramid Matching"
subtitle:   "ScSPM"
date:       2017-07-24 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 全局表示
---

> 本文涉及的论文：
> * 《Linear Spatial Pyramid Matching Using Sparse Coding for Image Classification》

由于BoF（bag-of-features）忽略了局部描述子的空间顺序，其限制了图像表示的描述能力，因而有了SPM（Spatial Pyramid Matching）的出现。SPM将图像在不同的尺度下$$(l=0,1,2)$$分为$$2^{l} \times 2^{l}$$个块，在$$21$$个块中分别计算BoF直方图，最后连接所有的直方图组成图像的向量表示。当$$l=0$$时，SPM退化为BoF。

本文是SPM方法的扩展，我们计算一个基于SIFT特征稀疏编码的空间金字塔图像表示，来代替传统SPM中的K-means向量量化（VQ），如下图所示：

![SPM与ScSPM](/img/spm/SPM-and-ScSPM.png)

### 使用SIFT稀疏编码的线性SPM

##### SIFT编码：从VQ到SC

$$X$$是在$$D$$维特征空间的SIFT描述子的集合，$$X=[x_{1},...,x_{M}]^{T} \in \mathbb{R}^{M \times D}$$。VQ方法使用K-means聚类算法求解以下问题：

$$\underset{V}{min} \sum_{m=1}^{M}\underset{k=1...K}{min}\left\|x_{m}-v_{k}\right\|^{2}\ \ \ \ (1)$$

$$V=[v_{1},...,v_{K}]^{T}$$是K个聚类中心，$$\left\| \cdot \right\|$$表示向量的L2范数。

上述优化问题可以重写为带有$$U=[u_{1},...,u_{M}]^{T}$$的矩阵因式分解问题：

$$\underset{U,V}{min}\sum_{m=1}^{M}\left\|x_{m}-u_{m}V\right\|^{2}\ \ \ \ (2)$$

$$subject\ to \ \ \ \ Card(u_{m})=1,|u_{m}|=1,u_{m}\geqslant 0,\forall m$$

$$Card(u_{m})=1$$是基数约束，即$$u_{m}$$中只有一个元素非$$0$$，$$u_{m}\geqslant 0$$意味着$$u_{m}$$中所有的元素都非负。$$u_{m}$$中唯一一个非零元素的下标对应着向量$$x_{m}$$所属的聚类中心。

约束$$Card(u_{m})=1$$过于限制，本文放宽这个约束而对$$u_{m}$$采用L1范数约束，其限制$$u_{m}$$只有少数的非零元素，则VQ公式可以转化成SC问题：

$$\underset{U,V}{min}\sum_{m=1}^{M}\left\|x_{m}-u_{m}V\right\|^{2}+\lambda |u_{m}|\ \ \ \ (3)$$

$$subject\ to\ \ \ \ \left\|v_{k}\right\|\leqslant 1, \forall k=1,2,...,K$$

字典V是过完备字典集，也就是$$K > D\$$

**SC相比VQ的优点：**

* 由于约束限制较少，SC可以达到更低的重建误差

* 稀疏性使得图像表示更专业，能捕捉到图像的显著特性

* 图像统计学的研究揭示了图像块是稀疏信号

##### 线性SPM

对于由一组描述子表示的图像，可以计算出一个基于描述子编码的统计特征向量。例如，通过$$(2)$$计算得到的$$U$$，可以计算其直方图：

$$z=\frac{1}{M}\sum_{m=1}^{M}u_{m}\ \ \ \ (4)$$

$$z_{i}$$表示图像$$I_{i}$$的直方图表示，对于图像二分类问题，SVM旨在学习以下决策函数

$$f(z)=\sum_{i=1}^{n}\alpha_{i}K(z,z_{i})+b$$

$$\{(z_{i},y_{i})\}^{n}_{i=1}$$是训练集，$$y_{i} \in \{-1,+1\}$$表明标签；理论上$$K(\cdot , \cdot)$$可以是任何合理的Mercer核函数，但实际上对于直方图表示最适合的是intersection核和Chi-square核；由于VQ的量化误差，用线性核分类效果不好，但是用以上的非线性核，SVM需要很高的训练代价，因此本文提出基于SIFT稀疏编码的线性SVM。

假设codebook $$V$$已经预训练并固定，可以通过以下预选择的池化函数来计算图像特征：

$$z=F(U)$$

不同的池化函数$$F$$构建不一样的图像统计特征，如$$(4)$$中的池化函数是平均函数，产生直方图特征；而本文定义的是最大池化函数：

$$z_{j}=max\{|u_{1j}|,|u_{2j}|,...,|u_{Mj}|\}\ \ \ \ (7)$$

$$z_{j}$$是$$z$$的第$$j$$个元素，$$u_{ij}$$是矩阵$$U$$的第$$i$$行第$$j$$列的元素，$$M$$是局部描述子的数目

与SPM中直方图的构建类似，我们在一个图像的空间金字塔构建中使用最大池化$$(7)$$，通过在图像的不同尺度不同位置最大池化，池化特征比直方图中的平均统计特征更robust

下图说明了基于稀疏编码的SPM的整个结构，不同尺度不同位置的池化特征将连结起来形成图像的空间金字塔表示：

![ScSPM结构示意图](/img/spm/ScSPM-structure.png)

图像$$I_{i}$$可以通过$$z_{i}$$表示，我们使用以下线性SPM核：

$$K(z_{i},z{j})=z_{i}^{T}z_{j}=\sum_{l=0}^{2} \sum_{s=1}^{2^{l}} \sum_{t=1}^{2^{l}} \left \langle z_{i}^{l}(s,t),z_{j}^{l}(s,t) \right \rangle\ \ \ \ (8)$$

$$\left \langle z_{i},z_{j} \right \rangle = z_{i}^{T}z_{j}$$，$$z_{i}^{l}(s,t)$$是图像$$I_{i}$$在level $$l$$尺度下第$$(s,t)$$个块的均方统计特征，因此二分类SVM决策函数是：

$$f(z)=\left ( \sum_{i=1}^{n}\alpha_{i}z_{i}\right )^{T}z+b=w^{T}z+b\ \ \ \ (9)$$

$$(5)$$是SVM的对偶形式，$$(9)$$是SVM的原始形式

### 算法实现

##### 稀疏编码

求解优化问题$$(3)$$，采用交替求解的方法，固定其中一个迭代求解另一个。固定$$V$$，可以通过单独优化每一个系数$$u_{m}$$：

$$\underset{u_{m}}{min}\left\|x_{m}-Vu_{m}\right\|_{2}^{2}+\lambda |u_{m}|\ \ \ \ (10)$$

这是一个在系数上L1范式正则化的线性回归问题，也就是统计学上的Lasso，可以通过比如feature-sign search算法求解该优化问题

固定$$U$$，问题退化为一个带有二次约束的最小二乘问题：

$$\underset{V}{min}\left\|X-VU\right\|^{2}_{F}\ \ \ \ (11)$$

$$s.t.\ \ \ \ \left\|v_{k}\right\| \leqslant 1, \forall k=1,2,...,K$$

该优化问题可以通过拉格朗日对偶形式解决

本文使用$$50000$$个提取自随机patch的SIFT描述子并通过迭代$$(10)$$和$$(11)$$来训练codebook，一旦线下训练得到codebook $$V$$，就可以通过$$(10)$$在线求解图像的每一个描述子

##### 多分类线性SVM

给定训练数据$$\{(z_{i},y_{i})\}_{i=1}^{n},y_{i}\in Y=\{1,...,L\}$$，线性SVM旨在学习L个线性函数

$$\{w_{c}^{T}z| c\in Y\}$$

因此对于一个训练数据$$z$$，它的类别标签是通过以下式子预测：

$$y=\underset{c\in Y}{max}\ \ w_{c}^{T}z$$

本文采用one-against-all策略训练L个二分类SVM，每一个解决以下无约束的凸优化问题：

$$\underset{w_{c}}{min}\left \{ J(w_{c})=\left\|w_{c}\right\|^{2}+C\sum_{i=1}^{n}l(w_{c};y_{i}^{c},z_{i}) \right \}$$

如果$$y_{i}=c$$则$$y_{i}^{c}=1$$，否则$$y_{i}^{c}=-1$$，$$l(w_{c};y_{i}^{c},z_{i})$$是hinge loss function，本文的hinge loss function如下：

$$l(w_{c};y_{i}^{c},z_{i})=[max(0,w_{c}^{T}z\cdot y_{i}^{c}-1)]^{2}$$

### 实验结果

**数据集**

* Caltech101

* Caltech 256

* 15 Scenes

* TRECVID 2008 surveillance video

**方法**

* KSPM：非线性SPM$$=$$空间金字塔直方图特征$$+$$Chi-square核

* LSPM：线性SPM$$=$$空间金字塔直方图特征$$+$$线性核

* ScSPM：线性SPM$$=$$SIFT稀疏编码的空间金字塔池化特征$$+$$线性核

本文只使用一种描述子类型：SIFT，从$$16\times 16$$的图像块提取的SIFT描述子是稠密地从每张图像上步长为$$8$$ pixels的grid上采样。所有图像被预处理成灰度图。训练codebook时，对KSPM和LSPM使用标准K-means聚类算法，而对ScSPM算法使用稀疏编码方案。除了TRECVID 2008，所有的实验我们固定codebook的大小以令其达到最优的实验结果，用于LSPM的codebook大小是$$512$$，用于ScSPM的codebook大小是$$1024$$。训练线性分类器时，采用多分类SVM。KSPM是使用LIBSVM库进行训练。

我们重复10次实验，使用不同的随机选取的训练集和测试集。

##### Caltech-101数据集

Caltech-101数据集包含$$101$$类（包含动物、交通工具、花朵等）。每一类别的图像数量从$$31$$到$$800$$不等，大多数图像都是中等分辨率（大概$$300 \times 300$$ pixels）。我们每一类采用$$15$$张和$$30$$张图像作为训练集，剩下的作为测试集，详细的实验结果如下图所示：

![Caltech-101实验结果](/img/spm/caltech-101-result.png)

##### Caltech-256数据集

Caltech-256中有$$29780$$张图片，$$256$$个类别。每一个类别至少有$$80$$张图片，我们分别在$$15,30,45$$和$$60$$张训练图像中使用ScSPM，其结果如下图所示：

![Caltech-256实验结果](/img/spm/caltech-256-result.png)

##### 15 Scenes Categorization

这个训练集包含$$4485$$张图像，$$15$$个类别，每一类图像的数量从$$200$$到$$400$$不等。和Lazebnik等人的实验过程一样，我们每一类使用$$100$$张图像作为训练集，其余的作为测试集，实验结果如下所示：

![15 Scenes实验结果](/img/spm/15-scenes-result.png)

##### TRECVID 2008 Surveillance Video

TRECVID 2008是$$100$$个小时的视频，NIST定义$$10$$类活动用以分类，提供$$50$$个小时的有标注的视频作为训练集，其余的$$50$$个小时作为测试集。一些活动如下所示：

![活动的例子](/img/spm/examples-of-events.png)

我们首先在每一帧上使用探测器检测出人，然后在检测区域中使用分类器。在训练阶段，我们将检测区域resize成$$100\times 100$$的图像，对于每一个样例，从$$16\times 16$$的图像块提取的SIFT描述子是从每张图像上步长为$$8$$ pixels的grid上采样。VQ和SC的codebook大小都是设置为$$256$$。实验结果如下：

![TRECVID的AUC结果](/img/spm/trecvid-2008-result.png)

### 实验回顾

##### Patch大小

在本实验中，我们仅仅采用一种patch大小去提取SIFT描述子，也就是$$16\times 16$$，在NBNN中，他们使用4中patch尺度去提取描述子以获取更好的实验结果，但是在本实验中并没有实质性的提高，可能是因为max pooling已经能获得局部区域的显著特征

##### Codebook大小

如果codebook size太小，直方图特征就会损失辨别能量；如果codebook size太大，同一类图像的直方图就会不匹配。在Lazebnik等人的工作中，他们使用size为$$200$$和$$400$$的codebook，其结果是差别不大。而我们的实验中对ScSPM和LSPM分别采用$$256,512$$和$$1024$$，其结果如下所示：

![codebook大小对实验结果的影响](/img/spm/codebook-size.png)

不难发现，ScSPM随着codebook大小的增加，其结果越好，而LSPM在512时达到较好的实验结果，在codebook为1024时，实验结果反而下降了。

##### 稀疏编码参数

$$(10)$$公式中有自由参数$$\lambda$$影响解的稀疏性。$$\lambda$$越大，则解越稀疏。从经验上来说，我们发现保持$$10%$$的稀疏性能获得好结果，在我们所有的实验中，我们设定$$\lambda$$的值是$$0.3~0.4$$，而非零参数的平均数量大概是10

##### 池化方法的比较

另外两种池化方法定义如下：

$$Sqrt:\ \ \ \ z{j}=\sqrt{\frac{1}{M} \sum_{i=1}^{M}u_{ij}^{2}}$$

$$Abs:\ \ \ \ z_{j}=\frac{1}{M} \sum_{i=1}^{M}|u_{ij}|$$

使用三种池化方法用于$$30$$张Caltech-100训练图像和$$100$$张15 Scenes训练图像，其结果如下所示：

![不同池化方法结果](/img/spm/pooling-result.png)

最大池化的效果最好

##### 线性核和非线性核的比较

我们在稀疏编码特征上使用intersection核和Chi-square核与线性核做比较，训练集是$$15$$张Caltech-101图像和15 Scenes图像，结果如下所示：

![线性核和非线性核比较结果](/img/spm/kernel-reuslt.png)

从实验结果可以看出，基于线性核的ScSPM取得较好的实验结果，一个直观的解释就是，具有稀疏特征的图案更线性可分。