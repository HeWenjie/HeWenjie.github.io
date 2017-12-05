---
layout:     post
title:      "随机洗牌"
subtitle:   "Random Shuffle"
date:       2017-12-05 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - C++
    - 算法
---

### 算法描述

随机化数组

### 算法代码

```
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;

vector<int> randomShuffle(vector<int> array) {
	for (int i = 0; i < array.size(); i++) {
		int j = rand() % (i + 1);
		swap(array[i], array[j]);
	}
	return array;
}
```