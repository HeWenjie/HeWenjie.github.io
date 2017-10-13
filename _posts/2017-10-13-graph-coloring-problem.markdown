---
layout:     post
title:      "图的着色问题"
subtitle:   "Graph Coloring Problem"
date:       2017-10-12 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 算法
    - C++
---

### 问题描述

给定一个无向图$$G$$和$$M$$种不同的颜色，求着色方案使得每一个节点着一种颜色且每一条边的两个节点着不同的颜色。

### 算法分析

图的着色问题可以用**回溯法**求解，首先解集是一个空集，依次选择节点着色。在前面$$n-1$$个节点都着色之后，开始对第$$n$$个节点着色，依次选择$$M$$种颜色，直到找到一种颜色使得$$n$$与其相邻的节点都不同色，则将节点$$n$$加入到解集中，当所有节点都着色后则能得到一种着色方案。

![无向图](/img/graph-coloring-problem/graph.png)

![回溯法](/img/graph-coloring-problem/backtrack.png)

### 算法代码

```
struct GCP {
	vector<vector<int>> adjMat;          // 图的邻接矩阵
	int v;                               // 图的顶点数目
	int m;                               // 可着的颜色数量
	vector<int> color;                   // 当前着色方案
	vector<vector<int>> allColor;        // 所有的着色方案
	GCP() {}
	GCP(int n, int m) {
		v = n;
		adjMat = vector<vector<int>>(n, vector<int>(n, 0));
		color = vector<int>(n, -1);
		this->m = m;
	}
};

bool isColoring(GCP G, int i) {
	for (int j = 0; j < i; j++) {
		if ((G.adjMat[i][j] == 1) && (G.color[j] == G.color[i])) {
			return false;
		}
	}
	return true;
}

void backtrack(GCP& G, int i) {
	if (i >= G.v) {

		for (int j = 0; j < G.color.size(); j++) {
			cout << G.color[j] << " ";
		}
		cout << endl;

		G.allColor.push_back(G.color);
	}
	else {
		for (int j = 0; j < G.m; j++) {
			G.color[i] = j;
			if (isColoring(G, i)) {
				backtrack(G, i + 1);
			}
		}
	}
}
```