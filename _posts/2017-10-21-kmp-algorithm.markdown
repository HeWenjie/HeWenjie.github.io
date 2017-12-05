---
layout:     post
title:      "KMP算法"
subtitle:   ""
date:       2017-10-21 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - C++
    - 算法
---

### 问题描述

字符串B是否是字符串A的子串？

### 问题分析

一个很容易想到的算法是遍历一遍字符串A，判断A中是否存在B这样的子串，其过程如下：

![朴素算法](/img/kmp-algorithm/naive-algorithm.png)

不难发现，其实模式串中，某个子串是模式串的前缀，比如上述例子中

![前缀](/img/kmp-algorithm/prefix.png)

由于在第$$1$$次匹配中，中间的子串是不匹配的，而中间的子串又是整个模式串的前缀，其实很容易就能知道第$$4$$次的匹配是肯定不会成功的。而KMP算法就能够跳过这些匹配过程从而加快匹配的速度。

### 算法介绍

KMP算法匹配过程如下：

![KMP算法](/img/kmp-algorithm/kmp-algorithm.png)

KMP算法的核心是next数组的建立，这里不讲原理，只讲next数组是怎么得到的

next数组其实是子串中前缀和后缀的最长公共匹配字符数

一个字符串的**前缀**就是删除字符串末端若干个（至少$$1$$个）连续的字符的得到的一系列子串，如：

![前缀例子](/img/kmp-algorithm/prefix-example.png)

**后缀同理**

则next数组的构建如下：

![next数组构造](/img/kmp-algorithm/next-example.png)

得到next数组如下：

![next数组](/img/kmp-algorithm/next-array.png)

当子串和模式串匹配时，如果某一个Index对应的字符不匹配，则将Index = next[Index - 1]

### 算法代码

```
// c++

#include <iostream>
#include <string>
#include <vector>

using namespace std;

// next数组的获取

vector<int> getNext(string pattern) {
	vector<int> next(pattern.length(), 0);

	int index = 0;

	for (int i = 1; i < pattern.length();) {
		if (pattern[i] == pattern[index]) {
			next[i] = index + 1;
			index++;
			i++;
		}
		else if (index != 0) {
			index = next[index - 1];
		}
		else {
			next[i] = 0;
			i++;
		}
	}

	return next;
}

bool kmp(string str, string pattern) {
	vector<int> next = getNext(pattern);
	int str_iter = 0;
	int pat_iter = 0;
	while (str_iter < str.length() && pat_iter < pattern.length()) {
		if (str[str_iter] == pattern[pat_iter]) {
			str_iter++;
			pat_iter++;
		} else if (pat_iter != 0) {
			pat_iter = next[pat_iter - 1];
		} else {
			str_iter++;
		}
	}

	if (pat_iter == pattern.length()) {
		return true;
	}

	return false;
}
```

### 算法优化

由上述算法，可以得到以下匹配过程

![kmp匹配过程](/img/kmp-algorithm/kmp-01.png)

不难发现，如果其子串ABD不匹配，那么其相同的前缀ABD必然不匹配，所以上述例子中第$$2$$次匹配是完全没有必要的

则匹配过程可以修改如下：

![KMP优化算法](/img/kmp-algorithm/kmp-02.png)

算法优化如下：

```
vector<int> temp = next;

for (int i = temp.size() - 1; i > 0; i--) {
	temp[i] = temp[i - 1];
}

temp[0] = 0;
next[0] = -1; // 当next == -1时，字符串下标也要+1

for (int i = 1; i < next.size(); i++) {
	if (pattern[i] == pattern[temp[i]]) {
		next[i] = next[temp[i]];
	}
	else {
		next[i] = temp[i];
	}
}
```

**完整代码如下**（注意KMP算法中对$$-1$$）的处理

```
// c++

#include <iostream>
#include <string>
#include <vector>
#include <stdlib.h>

using namespace std;

// next数组的获取

vector<int> getNext(string pattern) {
	vector<int> next(pattern.length(), 0);

	int index = 0;

	for (int i = 1; i < pattern.length();) {
		if (pattern[i] == pattern[index]) {
			next[i] = index + 1;
			index++;
			i++;
		}
		else if (index != 0) {
			index = next[index - 1];
		} else {
			next[i] = 0;
			i++;
		}
	}

	vector<int> temp = next;

	for (int i = temp.size() - 1; i > 0; i--) {
		temp[i] = temp[i - 1];
	}
	temp[0] = 0;

	next[0] = -1;
	
	for (int i = 1; i < next.size(); i++) {
		if (pattern[i] == pattern[temp[i]]) {
			next[i] = next[temp[i]];
		}
		else {
			next[i] = temp[i];
		}
	}

	return next;
}

bool kmp(string str, string pattern) {
	vector<int> next = getNext(pattern);
	int str_iter = 0;
	int pat_iter = 0;
	while (str_iter < str.length() && pat_iter < pattern.length()) {
		cout << str_iter << " " << pat_iter << endl;
		if (str[str_iter] == pattern[pat_iter]) {
			str_iter++;
			pat_iter++;
		}
		else if (pat_iter == 0) {
			str_iter++;
		}
		else if (pat_iter == -1) {
			pat_iter = 0;
			str_iter++;
		} else {
			pat_iter = next[pat_iter];
		}
	}

	if (pat_iter == pattern.length()) {
		return true;
	}

	return false;
}
```