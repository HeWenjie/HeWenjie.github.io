---
layout:     post
title:      "素数"
subtitle:   "prime number"
date:       2018-01-25 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - C++
    - 算法
---


### 快速判断一个数是否是素数

> 引用自http://blog.csdn.net/huang_miao_xin/article/details/51331710

质数分布规律：大于等于5的质数一定和6的倍数相邻

证明：假设$$x \geqslant 1$$，则自然数可以表示如下：

$$......, 6x-1, 6x, 6x+1, 6x+2, 6x+3, 6x+4, 6x+5, 6(x+1), 6(x+1)+1, ......$$

可以看到不与6的倍数相邻的数是$$6x+2, 6x+3, 6x+4$$，这些数可以表示为$$2(3x+1), 3(2x+1), 2(3x+2)$$，所以它们一定不是素数

```
bool isPrime(int n) {
    if (n == 2 || n == 3) {
        return true;
    }
    if (n % 6 != 1 && n % 6 != 5) {
        return false;
    }
    int tmp = sqrt(n);
    for (int i = 5; i <= tmp; i += 6) {
        if (n % i == 0 || n % (i + 2) == 0) {
            return false;
        }
    }
    return true;
}
```