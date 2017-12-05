---
layout:     post
title:      "回溯法"
subtitle:   "Backtracking"
date:       2017-10-15 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 算法
    - C++
---

### 算法介绍

回溯法就是将问题抽象成图或者树的结构，在包含问题所有解的解空间树中，从根节点出发按照深度优先搜索的策略遍历解空间树。当搜索到某一节点时判断该节点是否包含问题的解，如果包含则从当前节点继续遍历下去，如果不包含问题的解，则向其父节点回溯。

若要求出问题的所有可行解，则需要遍历所有可行子树算法才结束；若只需要求出一个可行解，则只要搜索到一个解就可以结束。

### 算法优化

当搜索解空间树的某一节点时，可以先用**剪枝**函数判断当前节点是否可行，如果当前节点不可行，则可以跳过其子树的搜索并向父节点回溯；否则按照深度优先搜索策略遍历其子树。

剪枝策略就是寻找过滤条件，提前减少不必要的搜索路径

### 经典问题

**[最大团问题](https://hewenjie.github.io/2017/10/12/maximum-clique-problem/)**

**[图的着色问题](https://hewenjie.github.io/2017/10/13/graph-coloring-problem/)**

### hihoCoder 1331

**扩展二进制数**

**问题描述：**

我们都知道二进制数的每一位可以是$$0$$或$$1$$。有一天小Hi突发奇想：如果允许使用数字$$2$$会发生什么事情？小Hi称其为扩展二进制数，例如$$(21)_{ii} = 2 * 2^{1} + 1 = 5, (112)_{ii} = 1 * 2^{2} + 1 * 2^{1} + 2 = 8$$。

很快小Hi意识到在扩展二进制中，每个数的表示方法不是唯一的。例如$$8$$还可以有$$(1000)_{ii}, (200)_{ii}, (120)_{ii}$$三种表示方法。

对于一个给定的十进制数$$N$$ ，小Hi希望知道它的扩展二进制表示有几种方法？

**输入**

一个十进制整数$$N$$。$$(0 \leqslant N \leqslant 1000000000)$$

**输出**

$$N$$的扩展二进制表示数目

**Example**

**Input**

```
8
```

**Output**

```
4
```

**AC**代码

```
// c++

#include <iostream>

#include <vector>

#include <cmath>

using namespace std;

int sum = 0;

// 获得当前条件下最大的数

int maxnum(int k, int i) {
	for (int j = 0; j <= i; j++) {
		k += 2 * pow(2, j);
	}
	return k;
}

// 回溯

void backtrack(int n, int i, vector<int> v, int k) {
	if (i < 0) {
		if (k == n) {
			sum++;
		}
	}

	// 剪枝：如果剩下未确定的数字全是2也比n要小，说明在当前分支不可能搜索到可行解

	else if (maxnum(k, i) >= n) {
		for (int j = 0; j < 3; j++) {
			v[i] = j;
			k += j * pow(2, i);
			if (k <= n) {
				backtrack(n, i - 1, v, k);
			}
			k -= j * pow(2, i);
		}
	}
}

int main() {
	
	int n;
	cin >> n;

	int i = 0;

	int temp = n;
	while (temp != 0) {
		temp /= 2;
		i++;
	}

	vector<int> v(i, 0);
	backtrack(n, i - 1, v, 0);

	cout << sum << endl;

	return 0;
}
```