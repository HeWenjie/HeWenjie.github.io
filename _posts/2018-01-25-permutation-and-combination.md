---
layout:     post
title:      "排列组合"
subtitle:   "permutation and combination"
date:       2018-01-25 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - C++
    - 算法
---

### 排列数

```
long factorial(long number)
{
    if (number <= 1)
        return 1;
    else
        return number*factorial(number - 1);
}

int permutation(int n, int m)
{
    int temp;
    if (n<m)
    {
        temp = n;
        n = m;
        m = temp;
    }
    return factorial(n) / factorial(n - m);
}
```

### 组合数

```
long factorial(long number)
{
    if (number <= 1)
        return 1;
    else
        return number*factorial(number - 1);
}
 
int combinator(int n, int m)
{
    int temp;
    if (n<m)
    {
        temp = n;
        n = m;
        m = temp;
    }
    return factorial(n) / (factorial(m)*factorial(n - m));
}
```
