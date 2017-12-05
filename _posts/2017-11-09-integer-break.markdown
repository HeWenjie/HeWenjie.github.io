---
layout:     post
title:      "整数分解"
subtitle:   "Integer Break"
date:       2017-11-09 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - C++
    - 算法
---

### 问题描述

给定一个整数$$N$$，将其分解成若干个数之和，要求这若干个数的乘积最大

即$$x_{1}+x_{2}+...+x_{n}=N,f(N)=max(x_{1}x_{2}...x_{n})$$

### 算法描述

**均值不等式：**$$H_{n} \leqslant G_{n} \leqslant A_{n} \leqslant Q_{n}$$

其中：

**调和平均数：**$$H_{n}=\frac{n}{\sum_{i=1}^{n}\frac{1}{x_{i}}}=\frac{n}{\frac{1}{x_{1}}+\frac{1}{x_{2}}+...+\frac{1}{x_{n}}}$$

**几何平均数：**$$G_{n}=\sqrt[n]{\prod_{i=1}^{n}x_{i}}=\sqrt[n]{x_{1}x_{2}...x_{n}}$$

**算术平均数：**$$A_{n}=\frac{\sum_{i=1}^{n}x_{i}}{n}=\frac{x_{1}+x_{2}+...+x_{n}}{n}$$

**平方平均数：**$$Q_{n}=\sqrt{\frac{\sum_{i=1}^{n}x_{i}^{2}}{n}}=\sqrt{\frac{x_{1}^{2}+x_{2}^{2}+...+x_{n}^{2}}{n}}$$

当且仅当$$x_{1}=x_{2}=...=x_{n}$$时取等号

由于**几何平均数**$$\leqslant$$**算术平均数**，则有

$$\frac{\sum_{i=1}^{n}x_{i}}{n}=\frac{x_{1}+x_{2}+...+x_{n}}{n} \leqslant \sqrt[n]{\prod_{i=1}^{n}x_{i}}=\sqrt[n]{x_{1}x_{2}...x_{n}}$$

也就是说：

$$x_{1}x_{2}...x_{n}\leqslant \left (\frac{x_{1}+x_{2}+...+x_{n}}{n}\right )^{n}$$

当且仅当$$x_{1}=x_{2}=...=x_{n}$$时取等号，则有$$x_{1}+x_{2}+...+x_{n}=n\bar{x}=N$$

所以$$x_{1}=x_{2}=...=x_{n}=\bar{x}=\frac{N}{n}$$

现在$$x_{1}x_{2}...x_{n}=\left ( \frac{N}{n}\right )^{n}$$

两边取对数并求导，则有

$$(ln(x_{1}x_{2}...x_{n}))' = ln(\frac{N}{n}) - 1$$

也就是$$ln(\frac{N}{n}) - 1 = 0$$时取得最大值，$$n=\frac{N}{e}$$

所以$$x_{1}=x_{2}=...=x_{n}=\frac{N}{n}=e$$

也就是将整数拆分成$$\frac{N}{e}$$个$$e$$时，其乘积取得最大值

### LeetCode 343

**Integer Break**

**问题描述**

Given a positive integer $$n$$, break it into the sum of at least two positive integers and maximize the product of those integers. Return the maximum product you can get.

For example, given $$n = 2$$, return $$1 (2 = 1 + 1)$$; given $$n = 10$$, return $$36 (10 = 3 + 3 + 4)$$.

Note: You may assume that $$n$$ is not less than $$2$$ and not larger than $$58$$.

**问题分析**

由于题目要求只能将$$N$$分解成正整数，而$$3$$是距离$$e$$最近的正整数，所以本题就是将$$N$$分解成若干个$$3$$和余数之和

注意到当余数是$$1$$时，也就是$$N = 3n + 1$$，由于$$1 \cdot 3^{n} < 4\cdot 3^{n-1}$$，所以要将其中一个$$3$$与余数$$1$$组合形成$$4$$

**AC**代码：

```
class Solution {
public:
    int integerBreak(int n) {
        if (n == 1) return 0;
        if (n == 2) return 1;
        if (n == 3) return 2;
        
        int num = n / 3;
        int remainder = n % 3;
        if (remainder == 0) {
            return pow(3, num);
        } else if (remainder == 1) {
            return pow(3, num - 1) * 4;
        } else {
            return pow(3, num) * 2;
        }
    }
};
```