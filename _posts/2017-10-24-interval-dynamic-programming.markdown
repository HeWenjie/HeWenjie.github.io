---
layout:     post
title:      "区间动态规划"
subtitle:   "Interval Dynamic Programming"
date:       2017-10-23 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - C++
    - 算法
---

### 算法介绍

区间动态规划问题是分治思想的一种应用。对于一个区间，其最优值由若干个更小的区间最优值得到。其实就是将一个区间问题划分为更小的区间直至一个不能更小的区间（一般是一个元素组成的区间），然后按照一定的规则合并并得到最优值，直到合并为原问题区间，则得到的最优值即为整个问题区间的解。

### 问题分析

##### hihoCoder 1338

**A Game**

**问题描述**

Little Hi and Little Ho are playing a game. There is an integer array in front of them. They take turns (Little Ho goes first) to select a number from either the beginning or the end of the array. The number will be added to the selecter's score and then be removed from the array.

Given the array what is the maximum score Little Ho can get? Note that Little Hi is smart and he always uses the optimal strategy.

**输入**

The first line contains an integer $$N$$ denoting the length of the array. $$(1 \leqslant N \leqslant 1000)$$

The second line contains $$N$$ integers $$A_{1},A_{2}, ..., A_{N}$$, denoting the array. $$(-1000 \leqslant A_{i} \leqslant 1000)$$

**输出**

Output the maximum score Little Ho can get.

**Example**

**Input**

```
4
-1 0 100 2
```

**Output**

```
99
```


**问题分析**

这题的动态规划公式如下：

$$$$

当$$i=j$$时其实是将题目划分为每一个最小的区间，也就是每个元素一个区间，根据题意，只有一个元素的时候肯定就是先手得分，所以$$dp(i, j)=a_{i}$$，也就是下标为$$i$$的元素。

当$$i\neq j$$时，有两种选择：

**（1）**选择数组头元素，根据题意，当先手选择数组头元素后，后手也将会以最优选择策略来选取剩下的数组元素，也就是后手在剩下的数组中得到的最优值是$$dp(i + 1, j)$$

**（2）**选择数组尾元素，根据题意，当先手选择数组尾元素后，后手也将会以最优选择策略来选取剩下的数组元素，也就是后手在剩下的数组中得到的最优值是$$dp(i, j - 1)$$

由于原本的区间数值之和是$$sum(i,j)$$，所以先手选择头元素后其值是$$sum(i, j) - dp(i + 1, j)$$，同理，选择尾元素后其值是$$sum(i, j) - dp(i, j - 1)$$

因此选择的最优策略就是$$max(sum(i,j)-dp(i+1, j), sum(i,j)-dp(i,j-1))$$

**AC**代码

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main() {
	int n;
	cin >> n;

	vector<vector<int>> f = vector<vector<int>>(n, vector<int>(n, 0));
	vector<int> sum = vector<int>(n, 0);

	for (int i = 0; i < n; i++) {
		cin >> f[i][i];
		if (i == 0) {
			sum[0] = f[i][i];
		}
		else {
			sum[i] = sum[i - 1] + f[i][i];
		}
	}


	for (int i = 1; i < n; i++) {
		for (int j = 0;; j++) {
			if (j + i >= n) break;
			if (j == 0) {
				f[j][j + i] = max(sum[j + i] - f[j][j + i - 1] , sum[j + i] - f[j + 1][j + i]);
			}
			else {
				f[j][j + i] = max(sum[j + i] - sum[j - 1] - f[j][j + i - 1], sum[j + i] - sum[j - 1] - f[j + 1][j + i]);
			}

		}
	}

	cout << f[0][n - 1] << endl;

	return 0;
}
```

##### 石头归并问题

