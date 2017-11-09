---
layout:     post
title:      "最大团问题"
subtitle:   "Maximum Clique Problem"
date:       2017-10-12 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 算法
    - C++
---

### 相关概念描述

**无向图：**对于一个无向图$$G=(V,E)$$，其中$$V$$是顶点集合，$$E$$是边集合，无向图中的边都是顶点的无序对。

**完全子图：**如果$$U \subseteq V$$，且对任意两个顶点$$u,v\in U$$都有$$(u,v)\in E$$，则$$U$$是$$G$$的完全子图（完全图中任意两个顶点之间都有边）

**团：**完全子图$$U$$是$$G$$的团当且仅当$$U$$不包含在$$G$$的更大的完全子图中。包含顶点数量最多的团称为最大团

**空子图：**如果$$U \subseteq V$$，且对任意两个顶点$$u,v\in U$$都有$$(u,v)\notin E$$，则$$U$$是$$G$$的空子图（空子图中任意两个顶点之间都没有边）。

**独立集：**空子图$$U$$是$$G$$的独立集当且仅当$$U$$不包含在$$G$$的更大的空子图中。包含顶点数量最多的独立集称为最大独立集。

**补集：**对于无向图$$G$$，其补集定义为$$G'=(V',E'),V'=V,(u,v)\in E'$$当且仅当$$(u,v)\notin E$$（如果$$U$$是$$G$$的完全子图，$$U$$也是$$G'$$的空子图；如果$$U$$是$$G$$的最大团，$$U$$也是$$G'$$的最大独立集）

![无向图G](/img/maximum-clique-problem/graph.png)

如图，$$\{1,2\}$$是$$G$$的一个完全子图，但$$\{1,2\}$$不是一个团，因为$$\{1,2\}$$中的顶点$$1、2$$包含在更大的完全子集$$\{1,2,3\}$$中。$$\{1,2,3\}$$、$$\{1,3,4\}$$和$$\{2,3,5\}$$都是一个团，且都是最大团。

### 问题解法

最大团问题是图论中一个经典的组合优化问题。也就是在一个无向图中找出一个点数最多的完全图。

无向图$$G$$的最大团问题可以用**回溯法**求解，最大团问题可以看作是顶点集的子集选取问题，首先团是一个空集，对于每一个顶点，都有两种选择，将其加入到团中或直接舍弃。某个顶点能加入到团中的条件是当前顶点加入团中仍然能构成一个团。这里的搜索策略中添加剪枝策略，就是**如果剩余的顶点数+现团中顶点数$$\leqslant$$当前最大团的顶点数**

![回溯示意图](/img/maximum-clique-problem/backtrack.png)

### 算法代码

```c++
// 最大团
struct MCP {
	vector<vector<int>> adjMat;    // 图的邻接矩阵
	int v;                         // 图的顶点数目
	vector<int> clique;            // 当前团中的节点
	vector<int> bestClique;        // 其中一个最优解
	int cnum;                      // 当前团中的节点数
	int bestn;                     // 最大团节点数
};

void backtrack(MCP &G, int i) {
	// 所有节点已经遍历完
	if (i >= G.v) {
		for (int j = 0; j < G.v; j++) {
			// 记录当前解的最大团
			G.bestClique[j] = G.clique[j];
		}
		G.bestn = G.cnum;
		return;
	}

	bool flag = true;
	for (int j = 0; j < i; j++) {
		// 判断当前节点是否还能和原团中节点构成一个新的团
		if (G.clique[j] && G.adjMat[i][j] == 0) {
			flag = false;
			break;
		}
	}
	if (flag) {
		G.clique[i] = 1;
		G.cnum++;
		backtrack(G, i + 1);
		// 回溯
		G.clique[i] = 0;
		G.cnum--;
	}
	// 剪枝
	if (G.cnum + G.v - (i + 1) > G.bestn)  {
		G.clique[i] = 0;
		backtrack(G, i + 1);
	}
}
```

### hihoCoder 1334

**Word Construction**

**问题描述：**

Given $$N$$ words from the top $$100$$ common words in English (see below for reference), select as many words as possible as long as no two words share common letters.

Assume that the top $$100$$ common words in English are:

the be to of and a in that have i it for not on with he as you do at this but his by from they we say her she or an will my one all would there their what so up out if about who get which go me when make can like time no just him know take people into year your good some could them see other than then now look only come its over think also back after use two how our work first well even new want because any these give day most us

**输入**

The first line contains an integer $$N$$, denoting the number of words. $$(1 \leqslant N \leqslant 40)$$  

The second line contains $$N$$ words from the top $$100$$ common words.

**输出**

Output the most number of words can be selected.

**Example**

**Input**

```c++
8
the be to of and a in that
```

**Output**

```c++
4
```

**AC**代码：

```c++
#include <iostream>
#include <string>
#include <sstream>
#include <vector>
#include <map>

using namespace std;

struct MCP {
	vector<vector<int>> adjMat;
	int v;
	vector<int> clique;
	vector<int> bestClique;
	int cnum;
	int bestn;
};

void backtrack(MCP &G, int i) {
	if (i >= G.v) {
		for (int j = 0; j < G.v; j++) {
			G.bestClique[j] = G.clique[j];
		}
		G.bestn = G.cnum;
		return;
	}

	bool flag = true;
	for (int j = 0; j < i; j++) {
		if (G.clique[j] && G.adjMat[i][j] == 0) {
			flag = false;
			break;
		}
	}
	if (flag) {
		G.clique[i] = 1;
		G.cnum++;
		backtrack(G, i + 1);
		G.clique[i] = 0;
		G.cnum--;
	}
	if (G.cnum + G.v - (i + 1) > G.bestn)  {
		G.clique[i] = 0;
		backtrack(G, i + 1);
	}
}

bool isConnected(string a, string b) {
	vector<int> letter(26, 0);
	for (int i = 0; i < a.size(); i++) {
		letter[a[i] - 'a'] = 1;
	}

	for (int i = 0; i < b.size(); i++) {
		if (letter[b[i] - 'a'] == 1) {
			return false;
		}
	}
	return true;
}

int main() {
	MCP G;
	cin >> G.v;
	getchar();
	string str;
	getline(cin, str);
	istringstream is(str);
	vector<string> dic;
	while (is >> str) {
		dic.push_back(str);
	}
	G.adjMat = vector<vector<int>>(dic.size(), vector<int>(dic.size(), 0));

	for (int i = 0; i < dic.size(); i++) {
		for (int j = 0; j < dic.size(); j++) {
			if (isConnected(dic[i], dic[j])) {
				G.adjMat[i][j] = 1;
				G.adjMat[j][i] = 1;
			}
		}
	}
	G.clique = vector<int>(dic.size(), 0);
	G.bestClique = vector<int>(dic.size(), 0);
	G.cnum = 0;
	G.bestn = 0;

	backtrack(G, 0);

	cout << G.bestn << endl;

	return 0;
}
```