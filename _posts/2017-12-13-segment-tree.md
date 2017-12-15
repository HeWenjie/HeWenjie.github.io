---
layout:     post
title:      "线段树"
subtitle:   "Segment Tree"
date:       2017-12-13 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - C++
    - 算法
---

### 问题描述

线段树是一种数据结构，可用于处理具有以下操作的问题：

1. 修改第$$i$$个数的值

2. 查询区间$$[a, b]$$的总和/最大值/最小值等

### hihoCoder 1077

**求区间最小值**

```
#include <iostream>
#include <vector>
#include <cmath>
#include <algorithm>
#define INF 0x4f4f4f4f

using namespace std;

struct Node {
	int value;
	int left_interval; // 左区间
	int right_interval; // 右区间
	Node() {
		value = 0;
		left_interval = -1;
		right_interval = -1;
	}
	Node(int val, int left, int right) {
		value = val;
		left_interval = left;
		right_interval = right;
	}
};

vector<Node> segment_tree; // 线段树
vector<int> leaf_index; // 记录叶子节点在线段树中的下标（叶子节点是区间为[A,A]的点）
vector<int> arr; // 输入数列
int min_ele; // 最小值

void Build(int left, int right, int index) {
	segment_tree[index].value = *min_element(arr.begin() + left, arr.begin() + right + 1);
	segment_tree[index].left_interval = left;
	segment_tree[index].right_interval = right;
	if (left == right) {
		leaf_index[left] = index;
		return;
	}
	Build(left, (left + right) / 2, index * 2 + 1);
	Build((left + right) / 2 + 1, right, index * 2 + 2);
}

int Query(int left, int right, int index) {
	if (segment_tree[index].left_interval == left && segment_tree[index].right_interval == right) {
		return min_ele = min(segment_tree[index].value, min_ele);
	}
	// 
	if (left <= segment_tree[index * 2 + 1].right_interval) {
		// 查询区间完全在节点的左子节点
		if (right <= segment_tree[index * 2 + 1].right_interval) {
			Query(left, right, index * 2 + 1);
		}
		// 查询区间在左右子节点中都有
		else {
			Query(left, segment_tree[index * 2 + 1].right_interval, index * 2 + 1);
			Query(segment_tree[index * 2 + 2].left_interval, right, index * 2 + 2);
		}
	}
	// 查询区间完全在节点的右子节点
	else {
		Query(left, right, index * 2 + 2);
	}
}

void Update(int index, int val) {
	// 叶子节点直接更新值
	if (segment_tree[index].left_interval == segment_tree[index].right_interval) {
		segment_tree[index].value = val;
	}
	// 非叶子节点更新为左右子节点中的较小值
	else {
		segment_tree[index].value = min(segment_tree[index * 2 + 1].value, segment_tree[index * 2 + 2].value);
	}
	// 遍历到根节点则停止遍历
	if (index != 0) {
		Update((index - 1) / 2, val);
	}
}

int main() {
	int N;
	scanf("%d", &N);
	arr = vector<int>(N, 0);

	for (int i = 0; i < N; i++) {
		scanf("%d", &arr[i]);
	}

	segment_tree = vector<Node>(pow(2, static_cast<int>(log2(N)) + 2) - 1, Node());
	leaf_index = vector<int>(N, -1);
	Build(0, N - 1, 0);

	int Q;
	scanf("%d", &Q);
	while (Q--) {
		int op;
		int left;
		int right;
		scanf("%d%d%d", &op, &left, &right);
		if (op == 0) {
			min_ele = INF;
			printf("%d\n", Query(left - 1, right - 1, 0));
		}
		else {
			Update(leaf_index[left - 1], right);
		}
	}
	return 0;
}
```