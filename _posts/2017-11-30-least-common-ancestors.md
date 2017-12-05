---
layout:     post
title:      "最近公共祖先"
subtitle:   "LCA"
date:       2017-11-30 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - C++
    - 算法
---

### 问题描述

求两个节点在树上深度最大的公共的祖先节点

### 算法介绍

##### RMQ问题

RMQ问题（Range Minimum/Maximum Query），即区间最值查询问题，求出给定区间的最大值/最小值

##### ST算法

ST算法是一个在线算法，其在$$O(nlogn)$$时间内进行预处理，在$$O(1)$$时间内回答每个查询

$$dp[i][j]$$表示从第$$i$$位开始连续$$2^{j}$$个数中的最值，即在区间$$[i, i + 2^{j} - 1]$$中的最值

状态转移方程如下：

$$dp[i][j] = min/max(dp[i][j - 1], dp[i + (1 << j - 1)][j-1])$$

代码如下:

```
vector<vector<int>> dp;
vector<vector<int>> index;

void SparseTable(vector<int> arr) {
	int nLog = int(log(double(arr.size())) / log(2.0));
	// dp存储区间的最值
	dp = vector<vector<int>>(arr.size(), vector<int>(nLog + 1, 0));
	// index存储区间最值的下标
	index = vector<vector<int>>(arr.size(), vector<int>(nLog + 1, 0));

	for (int i = 0; i < arr.size(); i++) {
		dp[i][0] = arr[i];
		index[i][0] = i;
	}

	for (int j = 1; j <= nLog; j++) {
		for (size_t i = 0; i + (1 << j) - 1 < arr.size(); i++) {
			int a = dp[i][j - 1];
			int b = dp[i + (1 << (j - 1))][j - 1];
			if (a <= b) {
				dp[i][j] = a;
				index[i][j] = index[i][j - 1];
			}
			else {
				dp[i][j] = b;
				index[i][j] = index[i + (1 << (j - 1))][j - 1];
			}
		}
	}
}

int query(int nStart, int nEnd) {
	int nLog = int((log(double(nEnd - nStart + 1)) / log(2.0)));
	int a = dp[nStart][nLog];
	int b = dp[nEnd - (1 << nLog) + 1][nLog];
	if (a <= b) {
		return index[nStart][nLog];
	}
	else {
		return index[nEnd - (1 << nLog) + 1][nLog];
	}
}
```

##### LCA

* **visit：**深度优先搜索的遍历顺序
* **first：**各节点在**visit**中首次出现的下标
* **deep：****visit**中各节点对应的树的高度
* **tree：**邻接表

则两个节点$$A$$和$$B$$的LCA就是，$$query(first[A], first[B])$$，而ST表是通过深度构造的，也就是$$SparseTable(deep)$$

### hihoCoder 1067/1069

**AC**代码:

```
#include <iostream>
#include <vector>
#include <string>
#include <map>
#include <algorithm>

using namespace std;

vector<int> first;
vector<int> visit;
vector<int> deep;
vector<vector<int>> tree;

vector<vector<int>> dp;
vector<vector<int>> index;


void SparseTable(vector<int> arr) {
	int nLog = int(log(double(arr.size())) / log(2.0));
	dp = vector<vector<int>>(arr.size(), vector<int>(nLog + 1, 0));
	index = vector<vector<int>>(arr.size(), vector<int>(nLog + 1, 0));

	for (int i = 0; i < arr.size(); i++) {
		dp[i][0] = arr[i];
		index[i][0] = i;
	}

	for (int j = 1; j <= nLog; j++) {
		for (size_t i = 0; i + (1 << j) - 1 < arr.size(); i++) {
			int a = dp[i][j - 1];
			int b = dp[i + (1 << (j - 1))][j - 1];
			if (a <= b) {
				dp[i][j] = a;
				index[i][j] = index[i][j - 1];
			}
			else {
				dp[i][j] = b;
				index[i][j] = index[i + (1 << (j - 1))][j - 1];
			}
		}
	}
}

int query(int nStart, int nEnd) {
	int nLog = int((log(double(nEnd - nStart + 1)) / log(2.0)));
	int a = dp[nStart][nLog];
	int b = dp[nEnd - (1 << nLog) + 1][nLog];
	if (a <= b) {
		return index[nStart][nLog];
	}
	else {
		return index[nEnd - (1 << nLog) + 1][nLog];
	}
}

void DFS(int root, int depth) {
	first[root] = deep.size();
	visit.push_back(root);
	deep.push_back(depth);
	if (tree[root].empty()) return;
	for (size_t i = 0; i < tree[root].size(); i++) {
		DFS(tree[root][i], depth + 1);
		deep.push_back(depth);
		visit.push_back(root);
	}
}

int main() {
	int N;
	cin >> N;

	map<string, int> name_index;
	map<int, string> index_name;
	tree = vector<vector<int>>(N + 1, vector<int>());
	map<string, bool> flag;
	int num = 0;
	while (N--) {
		string father;
		string son;
		cin >> father >> son;
		if (name_index.find(father) == name_index.end()) {
			name_index[father] = num;
			index_name[num] = father;
			flag[father] = true;
			num++;
		}
		if (name_index.find(son) == name_index.end()) {
			name_index[son] = num;
			index_name[num] = son;
			num++;
		}
		flag[son] = false;

		tree[name_index[father]].push_back(name_index[son]);
	}
	int root;
	for (map<string, bool>::iterator iter = flag.begin(); iter != flag.end(); iter++) {
		if (iter->second) {
			root = name_index[iter->first];
			break;
		}
	}
	first = vector<int>(name_index.size(), -1);
	DFS(root, 1);
	SparseTable(deep);

	int M;
	cin >> M;

	while (M--) {
		string father;
		string son;
		cin >> father >> son;

		int begin = first[name_index[father]];
		int end = first[name_index[son]];
		if (begin > end) swap(begin, end);

		int temp = visit[query(begin, end)];

		cout << index_name[temp] << endl;

	}
	return 0;
}

```