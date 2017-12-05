---
layout:     post
title:      "二分图匹配"
subtitle:   ""
date:       2017-11-17 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - C++
    - 算法
---

### 相关概念描述

**二分图：**无向图$$G=(V,E)$$，如果顶点集$$V$$可以划分为两个互不相交的子集$$A$$和$$B$$，并且对于边集$$E$$中的每一条边$$(i,j)$$，其顶点$$i$$和$$j$$分别属于$$A$$和$$B$$

![二分图](/img/bipartite-graph-matching/bipartite-graph.png)

**匹配：**图$$G$$的一个匹配$$M$$是$$G$$的一个子图，其中任意两条边都没有公共顶点，匹配$$M$$中点为匹配点，边为匹配边，下图都是上图$$G$$的一个匹配

![匹配](/img/bipartite-graph-matching/bipartite-graph-matching-01.png)

![匹配](/img/bipartite-graph-matching/bipartite-graph-matching-02.png)

**最大匹配：**一个图$$G$$的所有匹配中含有匹配边数目最多的匹配称为这个图的最大匹配。下图为上图$$G$$的最大匹配

![最大匹配](/img/bipartite-graph-matching/maximum-matching.png)

**完美匹配：**如果无向图$$G$$存在一个匹配$$M$$，使得无向图中的所有点都是匹配点，那么这个匹配就是无向图的一个完美匹配，**上图的最大匹配就是一个完美匹配。**但是不是所有的最大匹配都是完美匹配，如下：

![完美匹配](/img/bipartite-graph-matching/perfect-matching.png)

![非完美匹配](/img/bipartite-graph-matching/not-perfect-matching.png)

### 匈牙利算法

匈牙利算法用于寻找二分图的最大匹配。这里直接用例子来表示匈牙利算法是怎么实现的，依然用上述的无向图：

![无向图](/img/bipartite-graph-matching/undirected-graph.png)

* 先选择未匹配点$$a$$，发现与其相连的$$e$$也是未匹配点，因此将它们加入到匹配中

![匹配过程](/img/bipartite-graph-matching/matching-01.png)

* 选择下一个未匹配点$$b$$，发现与其相连的$$e$$已经是匹配点，寻找下一个与其相连的未匹配点，发现未匹配点$$g$$，将它们加入到匹配中

![匹配过程](/img/bipartite-graph-matching/matching-02.png)

* 选择下一个未匹配点$$c$$，发现唯一与其相连的$$g$$已经是匹配点，怎么办呢？发现$$g$$与$$b$$相连，尝试给$$b$$分配另一个未匹配点（蓝线为暂时拆掉的匹配）
  ![匹配过程](/img/bipartite-graph-matching/matching-03.png)
  发现另一个与$$b$$相连的$$e$$也已经是匹配点，再尝试将与$$e$$相连的$$a$$分配另一个未匹配点
  ![匹配过程](/img/bipartite-graph-matching/matching-04.png)
  发现$$a$$还可以与未匹配点$$f$$相连，则问题迎刃而解！将$$a$$和$$f$$相连，则$$b$$可以与$$e$$相连，最后$$c$$就可以与$$g$$相连
  ![匹配过程](/img/bipartite-graph-matching/matching-05.png)
  ![匹配过程](/img/bipartite-graph-matching/matching-06.png)
  ![匹配过程](/img/bipartite-graph-matching/matching-07.png)

* 选择下一个未匹配点$$d$$，发现与其相连的$$h$$也是未匹配点，将它们加入到匹配中

![匹配过程](/img/bipartite-graph-matching/matching-08.png)

* 无向图中未发现未匹配点，则算法结束，且这个最大匹配为完美匹配！如果还有未匹配点，且无法通过上述算法使得这个未匹配点加入到匹配中，则该最大匹配不是完美匹配

### 算法代码

```
bool find(int A[i]) {
	for (int j = 0; j < B.size(); j++) {
		// line[A[i]][B[j]] == true则说明A[i]与B[j]相连，flag[B[j]] == false表示没有另一个顶点A[x]尝试与B[j]匹配
		if (line[A[i]][B[j]] == true && flag[B[j]] == false) {
			flag[B[j]] = true;
			// belong[B[j]] == -1表示B[j]是未匹配点，find(belong[B[j]])是尝试给belong[B[j]]分配另一个未匹配点
			if (belong[B[j]] == -1 || find(belong[B[j]])) {
				belong[B[j]] = A[i];
				return true;
			}
		}
	}
	return false;
}
// -------------------------------------------------
// 在主程序中

int points = 0;

for (int i = 0; i < A.size(); i++) {
	flag = vector<int>(B.size(), 0);
	if (find(A[i])) {
		points++;
	}
}

```

### hihoCoder 1626

**AC**代码：

```
#include <iostream>
#include <string>
#include <vector>
#include <stdlib.h>

using namespace std;

bool find(string str, int index, vector<int>& belong, vector<int>& flag, vector<vector<int>> dic) {
	for (int i = 0; i < dic.size(); i++) {
		if (dic[i][str[index] - 'a'] && !flag[i]) {
			flag[i] = 1;
			if (belong[i] == -1 || find(str, belong[i], belong, flag, dic)) {
				belong[i] = index;
				return true;
			}
		}
	}
	return false;
}

int main() {
	int T;
	cin >> T;
	while (T--) {
		int N;
		cin >> N;

		string str;
		cin >> str;

		vector<vector<int>> dic(N, vector<int>(26, 0));

		for (int i = 0; i < N; i++) {
			string s;
			cin >> s;

			for (int j = 0; j < s.length(); j++) {
				dic[i][s[j] - 'a'] = 1;
			}
		}
		int match = 0;
		vector<int> belong(N, -1);
		for (int i = 0; i < str.length(); i++) {
			vector<int> flag(N, 0);
			if (find(str, i, belong, flag, dic)) {
				match++;
			}
		}
		if (match == str.length()) {
			cout << "Yes" << endl;
		}
		else {
			cout << "No" << endl;
		}
	}

	return 0;
}
```