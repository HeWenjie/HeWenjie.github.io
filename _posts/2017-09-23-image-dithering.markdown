---
layout:     post
title:      "图像抖动"
subtitle:   "Image Dithering"
date:       2017-09-23 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 计算机图形学
---

### 图像抖动的背景

由于我们通过眼睛观察一个包含几个像素的小区域时，眼睛往往是通过取整或将细节取平均而得到一个总体的强度效果。所以当一个输出设备的光强度范围较小时，可以将多个像素单元组合起来表示一种强度值，从而使可以得到的强度数目明显增加$$^{[1]}$$，这就是半色调技术，半色调技术通过色点大小或密度的变化造成总体强度的变化，从而表现出较多的对比度，也达到了用较少的色彩模拟表达较多色调的目的，而抖动技术就是不降低分辨率的半色调近似技术。

**原始灰度图像：**![灰度图](/img/image-dithering/gray-jinchen.png)

**半色调图像：**![抖动图](/img/image-dithering/dither-jinchen.png)

### 图像抖动方法

##### 随机抖动&按序抖动

抖动算法的关键是抖动矩阵的构造，在半色调处理的过程中，将灰度图中的点一一对应地映射至屏幕像素位置来生成光强度变化，抖动矩阵一般是$$n \times n$$的方阵，随机抖动和按序抖动的区别就是，随机抖动矩阵的值是通过随机得到的，而按序抖动矩阵的值一般是$$0~n^{2}-1$$之间的不同正整数，例如以下抖动矩阵

$$D_{2}=\begin{bmatrix}3 & 1\\ 0 & 2\end{bmatrix}$$

图像可以通过将输入强度与矩阵中的元素进行比较来决定显示的强度值，先将输入光强度比例变换至范围$$0\leqslant I\leqslant n^{2}$$中，若光强度$$I>D_{n}(j,k)$$（$$j$$和$$k$$分别是像素点$$x$$和$$y$$对应于抖动矩阵的行号和列号），则位置$$(x,y)$$的像素就是$$1$$，否则就是$$0$$，即

$$O(x,y)=\left\{\begin{matrix}1 & I(x,y)>M(j,k)\\ 0 & I(x,y)\leqslant M(j,k)\end{matrix}\right.$$

当$$O(x,y)=1$$输出白点，否则输出黑点。

通常高阶抖动矩阵可以由低阶矩阵通过迭代得到：

$$D_{n}=\begin{bmatrix} 4D_{\frac{n}{2}}+D_{2}(1,1)U_{\frac{n}{2}} & 4D_{\frac{n}{2}}+D_{2}(1,2)U_{\frac{n}{2}} \\ 4D_{\frac{n}{2}}+D_{2}(2,1)U_{\frac{n}{2}} & 4D_{\frac{n}{2}}+D_{2}(2,2)U_{\frac{n}{2}}\end{bmatrix}$$

有序抖动算法简单，也能形成较好的半色调图像质量，但是会含有明显的周期性纹理。

**随机抖动效果**

![抖动图](/img/image-dithering/random-dither-jinchen.png)

**$$D_{2}$$按序抖动效果**

![抖动图](/img/image-dithering/d2-order-dither-jinchen.png)

**$$D_{4}$$按序抖动效果**

![抖动图](/img/image-dithering/d4-order-dither-jinchen.png)

**$$D_{8}$$按序抖动效果**

![抖动图](/img/image-dithering/d8-order-dither-jinchen.png)

**抖动算法核心代码**

```matlab
for i = 1:height
    for j = 1:width
        if image_gray(i, j) > D(mod(i - 1, size(D, 1)) + 1, mod(j - 1, size(D, 1)) + 1)
            output_gray(i, j) = 1;
        else
            output_gray(i, j) = 0;
        end
    end
end
```

##### 误差分散

误差分散算法最早由Floyd和Steinberg于1976年首先提出$$^{[2]}$$，和抖动不一样，其不依赖抖动矩阵来决定图像的半色调，误差分散通过计算一个给定像素单元处的输入光强度值与显示像素强度之间的误差，并将误差分散到当前像素的右边或下方的像素中（假设是从左至右、从上到下扫描图像）。还有其他的误差分散算法将在下一部分详细说明。假设$$I(j,k)$$是当前像素位置，一个常见的误差分散如下：

$$\begin{cases}I(j,k+1)=I(j,k+1)+\frac{7}{16}*error\\ I(j+1,k-1)=I(j+1,k-1)+\frac{3}{16}*error\\ I(j+1,k)=I(j+1,k)+\frac{5}{16}*error\\ I(j+1,k+1)=I(j+1,k+1)+\frac{1}{16}*error\end{cases}$$

**误差分散效果（Floyd-Steinberg滤波器）**

![抖动图](/img/image-dithering/dither-jinchen.png)

**误差分散核心代码**将在算法详细介绍章节中介绍。

##### 点分散

点分散是误差分散方法的一种变形，该方法将原图像的强度值分为$$64$$类，矩阵值与显示强度之间的误差将仅仅分布至类号更大的相邻矩阵的元素中，这些类号分布的目标是，使那些被较低类号的元素完全包围的单元数目最小化，这就会使得周围元素的所有误差均集中到某个位置。计算机图形学中提供的一种类号可能的分布模式

$$\begin{bmatrix}34 &48  &40  &32  &29  &15  &23  &31 \\ 42 &58  &56  &53  &21  &5  &7  &10 \\50  &62  &61  &45  &13  &1  &2  &18 \\38  &46  &54  &37  &25  &17  &9  &26 \\28  &14  &22  &30  &35  &49  &41  &33 \\20  &4  &6  &11  &43  &59  &57  &52 \\12  &0  &3  &19  &51  &63  &60  &44 \\24  &16  &8  &27  &39  &47  &55  &36 \end{bmatrix}$$

### 算法详细介绍

下面我将结合代码详细介绍误差分散算法，除了传统的Floyd-Steinberg滤波器$$^{[2]}$$，还有Stucki滤波器$$^{[4]}$$、Sierra滤波器、Jarvis滤波器$$^{[5]}$$、Stevenson滤波器$$^{[6]}$$等，这些滤波器其实都是基于人眼的视觉特性：人眼对水平和垂直方向的敏感度要高于对角线方向的敏感度，各部分滤波器如下图所示：

**Floyd-Steinberg滤波器**

$$\begin{bmatrix} &\cdot  &7 \\ 3 & 5 & 1\end{bmatrix}$$

**Stucki滤波器**

$$\begin{bmatrix} &  & \cdot & 8 &4 \\2 &4  &8  &4  &2 \\1  &2  &4  &2  &1 \end{bmatrix}$$

**Sierra滤波器**

$$\begin{bmatrix} &  & \cdot &5  &3 \\2  &4  &5  &4  &2 \\  &2  &3  &2  & \end{bmatrix}$$

这里用**Floyd-Steinberg滤波器**来分析误差分散算法，代码如下

```matlab
image = imread(fullfile('.', 'jinchen.png'));

image_gray = double(rgb2gray(image));

[height, width] = size(image_gray);

output_gray = zeros(height, width);

for i = 1:height
    for j = 1:width
        if image_gray(i, j) > 128
            output_gray(i, j) = 255;
        else
            output_gray(i, j) = 0;
        end
        % 误差
        error = image_gray(i, j) - output_gray(i, j);
        % 判断是否越界
        if j < width
            image_gray(i, j + 1) = image_gray(i, j + 1) + error * (7 / 16);
        end
        % 判断是否越界
        if i < height
            image_gray(i + 1, j) = image_gray(i + 1, j) + error * (5 / 16);
            if  j < width
                image_gray(i + 1, j + 1) = image_gray(i + 1, j + 1) + error * (1 / 16);
            end
            if j > 1
                image_gray(i + 1, j - 1) = image_gray(i + 1, j - 1) + error * (3 / 16);
            end
        end
    end
end
```

**Stucki滤波器效果**

![抖动图](/img/image-dithering/Stucki-dither-jinchen.png)

一般来说会取灰度级的一半作为阈值，首先判断当前灰度级的大小，如果其大于$$128$$则输出图像相应位置像素点设置为$$255$$，否则为$$0$$。然后计算输出图像像素点值与输入图像像素点值的误差，在误差分散的过程中，（假设是从左到右、从上到下扫描）当像素点的右边或者下边没有像素点时，则不再分散误差，所以这里要用条件判断来判断是否已经越界。如果没有越界，则按照滤波器的模版来传播误差。以上介绍的都是灰度图的抖动算法，下面是RGB的误差分散算法，核心代码如下：

```matlab
for k = 1:dim
    for i = 1:height
        for j = 1:width
            if image(i, j, k) > 128
                output(i, j, k) = 255;
            else
                output(i, j, k) = 0;
            end
            error = image(i, j, k) - output(i, j, k);
            if j < width
                image(i, j + 1, k) = image(i, j + 1, k) + error * (7 / 16);
            end
            if i < height
                image(i + 1, j, k) = image(i + 1, j, k) + error * (5 / 16);
                if  j < width
                    image(i + 1, j + 1, k) = image(i + 1, j + 1, k) + error * (1 / 16);
                end
                if j > 1
                    image(i + 1, j - 1, k) = image(i + 1, j - 1, k) + error * (3 / 16);
                end
            end
        end
    end
end
```

可以看到，RGB图的误差传播算法和灰度图的并没有什么区别，只要分别结算R、G、B通道的误差分散图，最后恢复成RGB图像。

**原RGB图**

![RGB误差分散效果](/img/image-dithering/RGB-jinchen.png)

**RGB误差分散算法效果**

![RGB误差分散效果](/img/image-dithering/RGB-dither-jinchen.png)

### 参考文献

$$[1]$$ 计算机图形学（第三版），Donald Hearn、M. Pauline Baker，电子工业出版社

$$[2]$$ Floyd, Steinberg. An adaptive algorithm for spatial grayscale[J].SID, 1976, 17(2):75-77

$$[3]$$ Huang J.C, Bhattacharjya A. K. An adaptive halftone algorithm for composite documents[C]. Proceedings of SPIE, USA, 2004, 5293:425-433

$$[4]$$ Strucki P. A multiple-error correcting computation algorithm for bilevel image hard-copy reproduction[J]. IBM Technical Disclosure Bulletin, 1981,23(10):4433-4435

$$[5]$$ Jarvis J. F,Judice C. N,Ninke W. H. Survey of techniques for the display of continuous-tone pictures on bilevel displays[J]. Computer Graphics and Image Processing, 1976, 5(1):13-40

$$[6]$$ Stevenson R. L, Arce G. R. Binary display of hexagonally sampled continusous-tone image[J]. Journal of the optical society of America A(Optics and Image Science), 1985, 2(7):1009-1013