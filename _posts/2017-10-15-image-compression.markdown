---
layout:     post
title:      "图像压缩"
subtitle:   "Image Compression"
date:       2017-11-15 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 多媒体
---

### 17214510 何文杰

### 图像压缩背景

随着信息技术的发展，图像、声音等媒体信息的描述、存储和传输都是朝着数字化方向前进，图像信息被广泛应用于多媒体通信和计算机系统中。相比传统方式，数字图像有着传统方式无法比拟的优越性。但是大量的图像数据也要保证其传输质量、速度，同时还要考虑存储介质的大小容量等。图像压缩需要解决的问题就是如何在保证压缩后的重建图像能够被用户所接受的前提下，尽可能地对图像数据进行最大限度的压缩。


### 图像压缩概述

图像压缩其实是减少图像中的冗余信息。冗余信息主要是编码冗余、像素间冗余以及心理视觉冗余。其主要体现了以下两种特性：

1. **空间相关性**。一张图像内的任何一个场景都是由若干像素点构成的，某个像素通常与周围的某些像素在亮度和色度上存在一定关系，特别是在相邻像素之间、相邻行之间以及相邻帧之间都存在较强的空间相关性

2. **时间相关性**。视频其实是由一张张连续图像构成，每一张图像称为视频的一个帧，一个图像序列的前后帧之间也存在一定的关系

图像压缩的重要性主要体现在以下两个方面：

1. **数据存储方面**。虽然随着存储技术的发展，每单位面积存储介质可存储的数据也在呈几何级数上升，但是在当今大数据的背景下，要保存所有类型的数据仍非常困难，如果直接存储如此大的数据量而不经过压缩而存储在实际应用中是很难做到的。

2. **数据传输方面**。图像压缩可以帮助减少传输所需要的带宽，利用图像压缩可以将图像中的冗余信息去除，只保留少量的非相关信息进行传输，就可以大大节省传输所需要的带宽。

图像压缩的目的就是把原来较大的图像用尽量少的字节表示和传输，并且要求复原图像有较好的质量

### 图像压缩方法简介

图像压缩可以分为以下两种方法：

1. **有损压缩：**有损压缩是一种不可逆的压缩方法，其允许压缩过程中损失一定的信息。由于人类对图像或声波中的某些频率成分不敏感，利用这一特性，在不影响对原始图像理解的情况下可以得到较大的压缩比。

2. **无损压缩：**无损压缩是一种可逆的压缩方法，其要求重构的信号与原始信号完全一致。数据压缩后不会损失任何信息，其可以完全恢复到压缩前的状态，因此其压缩比通常小于有损压缩比。

### 有损压缩算法介绍

有损压缩利用了人们对图像或声波中的某些频率成分不敏感的特性，允许压缩过程中损失一定的信息。有损压缩的特点是保持颜色的逐渐变化，删除图像中颜色的突然变化。

##### 预测编码

预测编码是根据离散信号之间存在一定相关性的特点，利用前面一个或多个信号对下一个信号进行预测，然后对实际值和预测值的差（预测误差）进行编码。DPCM$$^{[1]}$$（Differential Pulse Code Modulation），差分脉冲编码调制是利用信号的相关性找出可以反映信号变化特征的一个差值量进行编码。

第$$n$$个信号$$x_{n}$$的熵满足：

$$H(x_{n}) \geqslant H(x_{n}|x_{n-1}) \geqslant H(x_{n}|x_{n-1}x_{n-2}) \geqslant ... \geqslant H(x_{n}|x_{n-1}x_{n-2}...x_{1})$$

$$n$$越大考虑更多元素之间的依赖关系时，熵值将进一步降低，得到的熵越接近于实际的熵值

$$\underset{n\leftarrow \infty}{lim}H_{n}(x)=\underset{n \leftarrow \infty}{lim}H_{n}(x_{n}|x_{n-1}x_{n-2}...x_{1})$$

因此参与预测的信号越多，预测就越准确。

##### 变换编码

变换编码的基本思想是将在通常的欧几里德几何空间（空间域）描写的图像信号映射变换到另外的向量空间（变换域）进行描述，然后再根据图像在变换域中系数的特点和人眼的视觉特性进行编码。

![变换编码](/img/image-compression/transform-coding.png)

变换编码包括傅里叶变换，DCT变换，DWT变换，哈尔变换等正交变换，一个正交变换是指变换的各个基向量之间相互正交，而变换就是把原来的信号分别投影到这些基向量上，每个基向量的系数就是投影值。因为正交的基向量之间没有相关性，所以变换后的系数之间不存在相关冗余信息。

##### 分形编码

分形编码$$^{[2,3]}$$是利用分形几何中的自相似的原理来实现的。首先对图像进行分块，然后再去寻找各块之间的相似性，相似性的描述主要是依靠仿射变换确定，并构造一个迭代函数系统（IFS）。利用IFS抽取图像的自相似性，即用图像中的一个子块经过自仿射变换来逼近同一图像中的另一子块。由于每块的数据量远大于仿射变换的系数，因而图像得以大幅度的压缩。

### 无损压缩算法介绍

##### 算术编码

算术编码使用$$0$$到$$1$$之间的实数进行编码。算术编码中的两个重要参数是符号的概率以及编码间隔。信源符号的概率决定压缩编码的效率，也决定编码过程中信源符号的间隔，而这些间隔包含在$$0$$到$$1$$之间。编码过程中的间隔决定了符号压缩后的输出。

##### 游长（RLE）编码

现实中有存在很多这样的图像，在一幅图像中具有许多颜色相同的图块。而在这些图块中，许多行上都具有相同的颜色，或者在一行上有许多连续的像素都具有相同的颜色值。在这种情况下其实并不需要存储每一个像素的颜色值，只需要记录一个像素的颜色值，以及具有相同颜色值的像素数目就可以。这种压缩方法就是游长编码。

![游长编码](/img/image-compression/RLE.png)

##### 词典编码

词典编码的根据是数据本身包含有重复代码的特性。

词典编码主要包含以下两类：

**1.** 查找正在压缩的字符序列是否曾经出现在以前的输入数据中，然后用已经出现过的字符串代替重复的部分，它的输出仅仅是指向早期出现过的字符串的“指针”
![词典编码](/img/image-compression/dictionary-coding-1.png)
**2.** 从输入的数据中创建一个词典，编码数据过程中当遇到已经在词典中出现的短语时，编码器就输出这个词典中的短语的索引号，而不是短语本身
![词典编码](/img/image-compression/dictionary-coding-2.png)

词典编码中最经典的算法就是LZ系列算法，包括LZ77算法$$^{[4]}$$、LZ78算法$$^{[5]}$$以及LZW算法$$^{[6]}$$。

下面简单介绍一下LZW算法，LZW算法首先建立一个字符串表，把每一个第一次出现的字符串放入字符串表中，并用一个数字来表示，这个数字与此字符串在串表中的位置有关，并将这个数字存入压缩文件中，如果这个字符串再次出现时，即可以用它的数字来代替，并将这个数字存入文件中。压缩完成后将字符串表丢弃。

**LZW编码伪代码**

```c++
Dictionary[j] = all n single-character, j = 1, 2, ..., n
j = n + 1
Prefix = read first Character in Charstream
while ( (C = next Character) != NULL )
	If Prefix.C is in Dictionary
		Prefix = Prefix.C
	else
		Codestream = cW for Prefix
		Dictionary[j] = Prefix.C
		j = n + 1
		Prefix = C
	end
Codestream = cW for Prefix
```

**LZW译码伪代码**

```c++
Dictionary[j] = all n single-character, j = 1, 2, ..., n
j = n + 1
cW = first code from Codestream
Charstream = Dictionary[cW]
pW = cW
While ( (cW = next Code word) != NULL )
	If cW is in Dictionary
		Charstream = Dictionary[cW]
		Prefix = Dictionary[pW]
		cW = first Character of Dictionary[cW]
		Dictionary[j] = Prefix.cW
		j = n + 1
		pW = cW
	else
		Prefix = Dictionary[pW]
		cW = first Character of Prefix
		Charstream = Prefix.cW
		Dictionary[j] = Prefix.C
		pW = cW
		j = n + 1
	end
```

### 经典算法

##### 有损压缩经典算法——JPEG

JPEG有损压缩算法$$^{[7]}$$是采用以离散余弦变换为基础的算法，其利用了人的视觉系统的特性，使用量化和无损压缩编码相结合来去掉视觉的冗余信息和数据本身的冗余信息。JPEG算法框图如下所示：

![JPEG算法框图](/img/image-compression/JPEG-algorithm.png)

可以看出JPEG算法主要分为三步：

1. 使用正向离散余弦变换把空间域表示的图变换成频率域表示的图

2. 使用加权函数对DCT系数进行量化

3. 使用哈夫曼可变字长编码器对量化系数进行编码

下面将详细介绍JPEG算法的计算步骤：

**1.** 正向离散余弦变换

**（1）**对每个单独的彩色图像分量，把整个分量图像分成$$8 \times 8$$的图像块并作为二维离散余弦变换DCT的输入（DCT变换能够把能量集中在少数的几个系数上，这样低频信号就集中在左上角，高频信号集中在右下角，有损压缩损失的就是量化过程中的高频部分）

**（2）**

**DCT变换公式**

$$F(u,v)=\frac{1}{4}C(u)C(v)\left [ \sum_{i=0}^{7}\sum_{j=0}^{7}f(i,j)cos\frac{(2i+1)u\pi}{16}cos\frac{(2j+1)v\pi}{16} \right ]$$

**IDCT变换公式**

$$f(u,v)=\frac{1}{4}C(u)C(v)\left [ \sum_{u=0}^{7}\sum_{v=0}^{7}F(u,v)cos\frac{(2i+1)u\pi}{16}cos\frac{(2j+1)v\pi}{16} \right ]$$

$$C(u),C(v)=\left\{\begin{matrix}\frac{1}{\sqrt{2}} & u,v=0 \\ 1 & others\end{matrix}\right.$$

**（3）**二维DCT可以通过下面计算式变成一维DCT

$$F(u,v)=\frac{1}{2}C(u)\left [ \sum_{i=0}^{7}G(i,v)cos\frac{(2i+1)u\pi}{16} \right ]$$

$$G(i,v)=\frac{1}{2}C(v)\left [ \sum_{j=0}^{7}f(i,j)cos\frac{(2j+1)v\pi}{16} \right ]$$

```matlab
% 得到8 x 8的DCT变换矩阵
DCT = dctmtx(8);
    
% 分别对Y、U、V做DCT变换
for i = 1:3
    func = @(block_struct) DCT * block_struct.data * DCT';
    DCTYUV(:, :, i) = blockproc(YUV(:, :, i), [8 8], func, 'PadPartialBlocks', true);
end
```

**2.** 量化

量化是对经过FDCT变换后的频率系数进行量化。量化的目的是减少非$$0$$系数的幅度以及增加$$0$$值系数的数目。JPEG算法使用均匀量化器进行量化，量化步距是按照系数所在的位置和每种颜色分量的色调值来确定

**亮度量化值**

![亮度量化值](/img/image-compression/brightness.png)

**色度量化值**

![色度量化值](/img/image-compression/chroma.png)

```matlab
% 量化
fun  = @(block_struct) round(block_struct.data ./ chromaTable);
DCTYUV(:, :, 1) = blockproc(DCTYUV(:, :, 1), [8 8], fun);
for i = 2:3
    fun  = @(block_struct) round(block_struct.data ./ brightnessTable);
    DCTYUV(:, :, i) = blockproc(DCTYUV(:, :, i), [8 8], fun);
end
```

**3.** Z字形编码

量化后的DCT系数要重新编排，目的是为了增加连续的$$0$$系数的个数（增加$$0$$的游长长度），方法是按照Z字形式样编排（低频分量先出现，高频分量后出现）

![Z字形编码](/img/image-compression/zigzag.png)

**4.** 直流系数的编码

$$8 \times 8$$图像块经过DCT变换之后得到的DC直流系数有两个特点：

1. 系数的数值比较大

2. 相邻$$8 \times 8$$图像块的DC系数值变化不大

根据这两个特点使用差分脉冲编码调制技术（DPCM）对相邻图像块之间的DC系数的差值进行编码：

$$\Delta = DC(0, 0)_{k}-DC(0,0)_{k-1}$$

```matlab
% DC分量（DPCM编码）
DC(1) = blocks(1, 1);
for j = 2:size(blocks)
    DC(j) = blocks(j, 1) - blocks(j - 1, 1);
end
```

**5.** 交流系数的编码

量化AC系数的特点是$$1 \times 64$$向量中包含有许多$$0$$系数，并且许多$$0$$是连续的，因此可以使用RLE编码对它们编码

JPEG使用$$1$$个字节的高$$4$$位来表示连续$$0$$的个数，使用低$$4$$位来表示编码下一个非$$0$$系数所需要的位数，跟在它后面的是量化AC系数的数值

```matlab
blocks = blocks(:, 2:end);
blocks = blocks(:);
    
index = 1;
count = 0;
for j = 1:size(blocks)
    if blocks(j) == 0
        count = count + 1;
        continue;
    else
        AC(index) = count;
        index = index + 1;
        AC(index) = blocks(j);
        index = index + 1;
        count = 0;
    end
end

% 特殊的(0,0)对
AC(index) = 0;
index = index + 1;
AC(index) = 0;
```

**6.** 熵编码

JPEG使用哈夫曼编码器来减少熵，哈夫曼编码器可以很方便地通过查表的方法来进行编码。压缩数据符号时，哈夫曼编码器对出现频率比较高的符号分配比较短的代码，对出现频率比较低的符号分配比较长的代码


**算法代码**

```matlab
% JPEG有损压缩

% IMG转JPEG
function [jpg] = img2jpg(img)

    % 色度量化值
    chromaTable = [16 11 10 16 24 40 51 61;
                   12 12 14 19 26 58 60 55;
                   14 13 16 24 40 57 69 55;
                   14 17 22 29 51 87 80 62;
                   18 22 37 56 68 109 103 77;
                   24 35 55 64 81 104 113 92;
                   49 64 78 87 103 121 120 101;
                   72 92 95 98 112 100 103 99];
    
    % 亮度量化值
    brightnessTable = [17 18 24 47 99 99 99 99;
                       18 21 26 66 99 99 99 99;
                       24 26 56 99 99 99 99 99;
                       47 66 99 99 99 99 99 99;
                       99 99 99 99 99 99 99 99;
                       99 99 99 99 99 99 99 99;
                       99 99 99 99 99 99 99 99;
                       99 99 99 99 99 99 99 99];
    
    % 块中元素的遍历顺序（Z字形编排）
    order = [1 2 9 17 10 3 4 11 ...
             18 25 33 26 19 12 5 6 ...
             13 20 27 34 41 49 42 35 ...
             28 21 14 7 8 15 22 29 ...
             36 43 50 57 58 51 44 37 ...
             30 23 16 24 31 38 45 52 ...
             59 60 53 46 39 32 40 47 ...
             54 61 62 55 48 56 63 64];

    % 得到图像的RGB三个通道
    RGB = double(img);
    
    image_size = size(RGB(:, :, 1));
       
    % 将图像由RGB空间转到YUV空间
    YUV(:, :, 1) = 0.299 * RGB(:, :, 1) + 0.587 * RGB(:, :, 2) + 0.114 * RGB(:, :, 3);
    YUV(:, :, 2) = -0.169 * RGB(:, :, 1) - 0.3316 * RGB(:, :, 2) + 0.5 * RGB(:, :, 3);
    YUV(:, :, 3) = 0.5 * RGB(:, :, 1) - 0.4186 * RGB(:, :, 2) - 0.0813 * RGB(:, :, 3);
    
    % 得到8 x 8的DCT变换矩阵
    DCT = dctmtx(8);
    
    % 分别对Y、U、V做DCT变换
    for i = 1:3
        fun = @(block_struct) DCT * block_struct.data * DCT';
        DCTYUV(:, :, i) = blockproc(YUV(:, :, i), [8 8], fun, 'PadPartialBlocks', true);
    end
    
    % 量化
    fun  = @(block_struct) round(block_struct.data ./ chromaTable);
    DCTYUV(:, :, 1) = blockproc(DCTYUV(:, :, 1), [8 8], fun);
    for i = 2:3
        fun  = @(block_struct) round(block_struct.data ./ brightnessTable);
        DCTYUV(:, :, i) = blockproc(DCTYUV(:, :, i), [8 8], fun);
    end

    for i = 1:3
        % 将图像划分为若干个块，每个块是8 x 8的大小
        blocks = im2col(DCTYUV(:, :, i), [8 8], 'distinct');
  
        block_num = size(blocks, 2);
        
        % 按照Z字形来编排每个块
        blocks = blocks(order, :);
        
        blocks = blocks';
        % DC分量（DPCM编码）
        DC(1) = blocks(1, 1);
        for j = 2:size(blocks)
            DC(j) = blocks(j, 1) - blocks(j - 1, 1);
        end
        
        % AC分量（RLE编码）
        blocks = blocks(:, 2:end);
        blocks = blocks(:);
        
        index = 1;
        count = 0;
        for j = 1:size(blocks)
            if blocks(j) == 0
                count = count + 1;
                continue;
            else
                AC(index) = count;
                index = index + 1;
                AC(index) = blocks(j);
                index = index + 1;
                count = 0;
            end
        end
        
        % 特殊的(0,0)对
        AC(index) = 0;
        index = index + 1;
        AC(index) = 0;
        
        % 保存原图像大小
        jpg{i}.image_size = image_size;
        
        % 保存DCT变换后图像的大小
        % （PadPartialBlocks == true，DCT图像大小可能不等于原图像大小）
        jpg{i}.dct_image_size = size(DCTYUV(:, :, i));
        
        % 图像块的数目
        jpg{i}.block_num = block_num;
        
        % 图像信息
        jpg{i}.DC = int16(DC);
        jpg{i}.AC = int16(AC);
                
    end
end

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

% JPEG转IMG
function [img] = jpg2img(jpg)

    % 色度量化值
    chromaTable = [16 11 10 16 24 40 51 61;
                   12 12 14 19 26 58 60 55;
                   14 13 16 24 40 57 69 55;
                   14 17 22 29 51 87 80 62;
                   18 22 37 56 68 109 103 77;
                   24 35 55 64 81 104 113 92;
                   49 64 78 87 103 121 120 101;
                   72 92 95 98 112 100 103 99];
    
    % 亮度量化值
    brightnessTable = [17 18 24 47 99 99 99 99;
                       18 21 26 66 99 99 99 99;
                       24 26 56 99 99 99 99 99;
                       47 66 99 99 99 99 99 99;
                       99 99 99 99 99 99 99 99;
                       99 99 99 99 99 99 99 99;
                       99 99 99 99 99 99 99 99;
                       99 99 99 99 99 99 99 99];

    % 块中元素的遍历顺序（Z字形编排）
    order = [1 2 9 17 10 3 4 11 ...
             18 25 33 26 19 12 5 6 ...
             13 20 27 34 41 49 42 35 ...
             28 21 14 7 8 15 22 29 ...
             36 43 50 57 58 51 44 37 ...
             30 23 16 24 31 38 45 52 ...
             59 60 53 46 39 32 40 47 ...
             54 61 62 55 48 56 63 64];
         
    % DCT变换矩阵
    DCT = dctmtx(8);

    % 恢复原顺序
    matrix_order = order;
    for k = 1:length(order)
        matrix_order(k) = find(order == k);
    end
     
    for i = 1:3
        block_num = jpg{i}.block_num;
        image_size = jpg{i}.image_size;
        dct_image_size = jpg{i}.dct_image_size;
        DC = double(jpg{i}.DC);
        AC = double(jpg{i}.AC);
        
        blocks = zeros(block_num, 64);
        
        % DC分量的恢复
        blocks(1, 1) = DC(1);
        for j = 2:size(DC, 2)
            blocks(j, 1) = blocks(j - 1, 1) + DC(j);
        end
        
        blocks = blocks(:);
        
        % AC分量的恢复
        index = 1;
        blocks_index = 1 + size(DC, 2);
        while (blocks_index <= size(blocks, 1))
            if AC(index) == 0
                index = index + 1;
                if AC(index) == 0
                    while (blocks_index <= size(blocks, 1))
                        blocks(blocks_index) = 0;
                        blocks_index = blocks_index + 1;
                    end
                    break;
                else
                    blocks(blocks_index) = AC(index);
                end
                index = index + 1;
            else
                temp = blocks_index + AC(index);
                while (blocks_index < temp)
                    blocks(blocks_index) = 0;
                    blocks_index = blocks_index + 1;
                end
                index = index + 1;
                blocks(blocks_index) = AC(index);
                index = index + 1;
            end
            blocks_index = blocks_index + 1;
        end

        blocks = reshape(blocks, block_num, 64);
        
        blocks = blocks';
        
        % 恢复原矩阵的顺序
        blocks = blocks(matrix_order, :);
        
        % 将原列向量还原为图像矩阵
        YUV(:, :, i) = col2im(blocks, [8 8], dct_image_size, 'distinct');
        
        if i == 1
            table = chromaTable;
        else
            table = brightnessTable;
        end
        
        fun = @(block_struct)block_struct.data .* table;
        YUV(:, :, i) = blockproc(YUV(:, :, i), [8 8], fun);
        
        fun = @(block_struct)DCT' * block_struct.data * DCT;
        YUV(:, :, i) = blockproc(YUV(:, :, i), [8 8], fun);
        
    end
    
    
    % 将图像由YUV空间转到RGB空间，需要把多余的部分切割掉
    
    YUV2(:,:,1) = YUV(1:image_size(1), 1:image_size(2), 1);
    YUV2(:,:,2) = YUV(1:image_size(1), 1:image_size(2), 2);
    YUV2(:,:,3) = YUV(1:image_size(1), 1:image_size(2), 3);
    
    RGB(:, :, 1) = YUV2(:,:,1) - 0.001 * YUV2(:,:,2) + 1.402 * YUV2(:,:,3);
    RGB(:, :, 2) = YUV2(:,:,1) - 0.344 * YUV2(:,:,2) - 0.714 * YUV2(:,:,3);
    RGB(:, :, 3) = YUV2(:,:,1) + 1.772 * YUV2(:,:,2) + 0.001 * YUV2(:,:,3);
    
    % 将RGB图像拼接起来
    img = cat(3, RGB(:, :, 1), RGB(:, :, 2), RGB(:, :, 3));
    img = uint8(img);
    
end
```

**实验结果**

**原图**

![金晨](/img/image-compression/RGB-jinchen.png)

**JPEG压缩重构图**

![JPEG金晨](/img/image-compression/jpeg-jinchen.png)

**信噪比**

这里采用PSNR来计算信噪比

$$PSNR=10\times log_{10}\left ( \frac{(2^{n} - 1)^{2}}{MSE} \right )$$

$$MSE$$是均方误差

RGB三个通道的PSNR分别是$$40.6374,41.3752,40.3066$$，平均PSNR是$$40.7731$$


##### 无损压缩经典算法——哈夫曼编码

哈夫曼编码使用变长编码表对源符号进行编码，其中变长编码表是通过评估来源符号出现几率得到的，出现几率高的符号分配比较短的代码，出现几率低的符号分配比较长的代码，这使得编码之后的字符串的平均长度、期望值降低。

哈夫曼编码的步骤：

**（1）**初始化，根据符号概率的大小按由大到小的顺序对符号进行排序

**（2）**把概率最小的两个符号组成一个节点

**（3）**重复步骤（2）直至形成一棵树

**（4）**从根节点到对应于每个符号的叶子节点，从上到下标上$$0$$或$$1$$（$$01$$的分配仅影响分配的结果，而代码的平均长度是相同的）

**（5）**从根节点开始顺着路径写出到每个叶子节点的每个符号的代码

![哈夫曼编码](/img/image-compression/huffman.png)

**哈夫曼编解码代码如下：**

```matlab

% 编码器
function encoder = huffmanEncoder(img)

	RGB = double(img);
    
    pixel_num = 256;

	for i = 1:3
		
        image_size = size(RGB(:, :, i));
		pixel = RGB(:, :, i);
		pixel = pixel(:);
		val = zeros(pixel_num, 1);

        % 统计各像素点出现的次数
        for j = 1:size(pixel)
			val(pixel(j) + 1) = val(pixel(j) + 1) + 1;
        end
        
        % 由于浮点数的运算不稳定，直接采用出现次数作为其概率
        [P, ind] = sort(val);
        
        index = 1;
        PT = zeros(pixel_num - 1, pixel_num);
        PT(index, :) = P;
        for j = pixel_num:-1:3
            index = index + 1;
            % 合并概率值最小的两个节点
            PT(index, 1) = P(1) + P(2);
            
            for k = 2:j-1
                PT(index, k) = P(k + 1);
            end
            % 合并节点后排序
            PT(index, 1:j - 1) = sort(PT(index, 1:j - 1));
            P = PT(index, 1:j - 1);
        end
        Code = cell(pixel_num - 1, pixel_num);
        Code(:, :) = {'0'};
        
        Code(index, 1) = {'1'};
        
        for j = (index - 1):-1:1
            % 两个节点合并之后的概率之和
            P_val = PT(j, 1) + PT(j, 2);
            % 寻找合并之后的概率和的下标，出现重复概率取第一个
            P_index = find(PT(j + 1, :) == P_val);
            P_index = P_index(1);
            
            % 将合并的Code传递给其子节点
            Code(j, 1:2) = Code(j + 1, P_index);
            % 左子节点添1，右子节点添0
            Code(j, 1) = strcat(Code(j, 1), '1');
            Code(j, 2) = strcat(Code(j, 2), '0');
            
            % 记录节点是否已经访问过
            visit = zeros(pixel_num, 1);
            visit_index = 1;
            visit(visit_index) = P_index;
            % 处理剩余节点
            for k = 3:(257 - j)
                % 查找PT(j, k)节点在下一行的位置
                P_index = find(PT(j, k) == PT(j + 1, :));
                % 对于每一个位置
                for l = 1:length(P_index)
                    if (isempty(find(visit(:) == P_index(l))))
                        visit_index = visit_index + 1;
                        Code(j, k) = Code(j + 1, P_index(l));
                        visit(visit_index) = P_index(l);
                        break;
                    end    
                end
            end 
        end

        Code = Code(1, :)';
        
        for j = 1:size(pixel, 1);
            if mod(j, 100000) == 0
                disp('正在编码.....');
            end
            data(j, 1) = Code(pixel(j) + 1 == ind(:));
        end
        
        encoder{i}.data = data;
        encoder{i}.Code = Code;
        encoder{i}.image_size = image_size;
        encoder{i}.ind = ind;
        
	end

end


%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

% 解码器
function [img] = huffmanDecoder(encoder)

    for i = 1:3
        data = encoder{i}.data;
        Code = encoder{i}.Code;
        image_size = encoder{i}.image_size;
        ind = encoder{i}.ind;
        
        pixel = zeros(size(data, 1), 1);
        
        for j = 1:size(data, 1)
            if mod(j, 100000) == 0
                disp('正在译码.....');
            end
            % 由于Code采用升序排列，越在后面的出现几率越大，倒序查找加快搜索速度
            for k = size(Code, 1):-1:1
                if strcmp(data{j}, Code{k}) == 1
                    pixel(j, 1) = ind(k) - 1;
                    break;
                end
            end
        end
        
        pixel = reshape(pixel, image_size(1), image_size(2));
        
        RGB(:, :, i) = pixel;
        
    end
    
    img = cat(3, RGB(:, :, 1), RGB(:, :, 2), RGB(:, :, 3));
    img = uint8(img);
    
end
```

**实验结果**

**原图**

![金晨](/img/image-compression/RGB-jinchen.png)

**哈夫曼压缩重构图**

![JPEG金晨](/img/image-compression/huffman-jinchen.png)

**信噪比**

这里采用PSNR来计算信噪比

$$PSNR=10\times log_{10}\left ( \frac{(2^{n} - 1)^{2}}{MSE} \right )$$

$$MSE$$是均方误差

当前huffman的信噪比是$$Inf$$，因为均方误差为$$0$$，说明huffman压缩是无损压缩

### 参考文献

**[1]** Van Baul M C W. Hybrid DPCM - a combination of PCM and DPCM. IEEE Trans Comman, 1978.26(3):362-368

**[2]** A. E. Jacauin. A Novel Fractal Blocking-coding Technique for Digital Image[C]. Proceeding of ICASSP-1990 IEEE International Conference on Acoustics, Speech and Signal Processing 1990. 2225-2228

**[3]** A. D. Sloan, Retrieving database contents by image recognition: New fractal power, Advanved Imaging, 1994, 9(5):5

**[4]** Ziv J, Lempel A. A Universal Algorithm for Sequential Data Compression. IEEE Transactions on Information Theory, 1977, 23(5)

**[5]** Ziv J, Lempel A. Compression of Individual Sequene via Variable-rate Coding. IEEE Transactions on Information Theory, 1978, 24(5):530-5363

**[6]** T. A. Welch. A technique for high-performance data compression. IEEE computer, Vol, 0-17, 8~19, June. 1984

**[7]** 林福宗，多媒体技术基础[M]第2版，北京：清华大学出版社，73-77