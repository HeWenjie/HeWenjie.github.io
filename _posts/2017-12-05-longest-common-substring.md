---
layout:     post
title:      "最长公共子串"
subtitle:   "Longest Common Substring"
date:       2017-12-06 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - C++
    - 算法
---

### 问题描述

找到两个字符串的最长公共子串

### 算法描述

假设$$A$$和$$B$$是两个字符串，且$$A$$和$$B$$的长度分别是$$n$$和$$m$$则：

* 如果$$A$$的最后一个元素$$=B$$的最后一个元素：$$LCS(A, B) = LCS(A.substr(0, n - 1), B.substr(0, m - 1)) + 1$$

* 如果$$A$$的最后一个元素$$\neq B$$的最后一个元素：$$LCS(A, B) = 0$$

则$$A$$和$$B$$的最长公共子串是LCS中的最大值

### 算法代码

```
#include <string>
#include <algorithm>
#include <vector>

using namespace std;

int LCS(string a, string b) {
	vector<vector<int>> LCSTable(a.size() + 1, vector<int>(b.size() + 1, 0));
	int maxLength = 0;
	for (int i = 0; i < a.size(); i++) {
		for (int j = 0; j < b.size(); j++) {
			if (a[i] == b[j]) {
				LCSTable[i + 1][j + 1] = LCSTable[i][j] + 1;
			}
			else {
				LCSTable[i + 1][j + 1] = 0;
			}
			if (LCSTable[i + 1][j + 1] > maxLength) {
				maxLength = LCSTable[i + 1][j + 1];
			}
		}
	}
	return maxLength;
}
```