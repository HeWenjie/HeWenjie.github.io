---
layout:     post
title:      "并查集"
subtitle:   "Union Find"
date:       2017-10-10 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 算法
    - C++
---

### 算法介绍

并查集用于处理一些不相交集合的合并及查询问题，查找两个元素是否属于同一个集合，合并不同的集合。

### 并查集代码

**查找**

每一个集合都用其根节点来代表，查找操作用于获得当前节点所在的集合，也就是返回当前节点所在集合的根节点

```c++
// 递归实现
template <class DATATYPE>
DATATYPE find(DATATYPE x) {
	if (father[x] == x) {
		return x;
	}
	else {
		father[x] = find(father[x]);
		return father[x];
	}
}
```

```c++
// 非递归实现
template <class DATATYPE>
DATATYPE find(DATATYPE name) {
	if (parent[name] == name) {
		return name;
	}
	else {
		DATATYPE temp = name;
		while (parent[temp] != temp) {
			temp = parent[temp];
		}
		parent[name] = temp;
		return parent[name];
	}
}
```

**合并**

将不同的集合合并，关键是找到两个集合的根节点，如果两个集合的根节点相同，说明其属于同一个集合，不需要合并，反之则合并两个集合。

![并查集合并操作](/img/union-find/union.jpg)

```c++
template <class DATATYPE>
void unite(DATATYPE a, DATATYPE b) {
	a = find(a);
	b = find(b);

	if (a == b) return;
	parent[b] = a;
}
```

### LeetCode 547

**问题描述**

There are $$N$$ students in a class. Some of them are friends, while some are not. Their friendship is transitive in nature. For example, if $$A$$ is a direct friend of $$B$$, and $$B$$ is a direct friend of $$C$$, then $$A$$ is an indirect friend of $$C$$. And we defined a friend circle is a group of students who are direct or indirect friends.

Given a $$N\times N$$ matrix $$M$$ representing the friend relationship between students in the class. If $$M[i][j] = 1$$, then the $$i$$th and $$j$$th students are direct friends with each other, otherwise not. And you have to output the total number of friend circles among all the students.

**Example 1**

```c++
Input: 
[[1,1,0],
 [1,1,0],
 [0,0,1]]
Output: 2
Explanation:The 0th and 1st students are direct friends, so they are in a friend circle. 
The 2nd student himself is in a friend circle. So return 2.
```

**Example 2**

```c++
Input: 
[[1,1,0],
 [1,1,1],
 [0,1,1]]
Output: 1
Explanation:The 0th and 1st students are direct friends, the 1st and 2nd students are direct friends, 
so the 0th and 2nd students are indirect friends. All of them are in the same friend circle, so return 1.
```

**AC**代码：

```c++
class Solution {
public:
    int findCircleNum(vector<vector<int>>& M) {
        for (int i = 0; i < M.size(); i++) {
            father.push_back(i);
        }
        
        for (int i = 0; i < M.size(); i++) {
            for (int  j = i + 1; j < M.size(); j++) {
                if (M[i][j] == 1) {
                    unite(i, j);
                }
            }
        }
        
        
        set<int> res;
        for (int i = 0; i < father.size(); i++) {
            father[i] = find(i);
            res.insert(father[i]);
        }
        
        return res.size();
    }
private:
    vector<int> father;
    int find(int x) {
        if (father[x] == x) {
            return x;
        } else {
            father[x] = find(father[x]);
            return father[x];
        }
    }
    void unite(int x, int y) {
        x = find(x);
        y = find(y);
        if (x == y) return;
        
        father[y] = x;
    }
};
```

### hihoCoder hiho一下 第171周

**问题描述：**

You are given a list of usernames and their email addresses in the following format:

alice 2 alice@hihocoder.com alice@gmail.com
bob 1 bob@qq.com
alicebest 2 alice@gmail.com alice@qq.com
alice2016 1 alice@qq.com

Your task is to merge the usernames if they share common email address:

alice alicebest alice2016
bob

**输入**

The first line contains an integer $$N$$, denoting the number of usernames. $$(1 < N \lesqlant 10000)$$

The following $$N$$ lines contain $$N$$ usernames and their emails in the previous mentioned format.

Each username may have $$10$$ emails at most.

**输出**

Output one merged group per line.

In each group output the usernames in the same order as the input.

Output the groups in the same order as their first usernames appear in the input.

**Example**

**Input**

```c++
4
alice 2 alice@hihocoder.com alice@gmail.com
bob 1 bob@qq.com
alicebest 2 alice@gmail.com alice@qq.com
alice2016 1 alice@qq.com
```

**Output**

```c++
alice alicebest alice2016
bob
```

**AC**代码：

```c++
#include <iostream>
#include <vector>
#include <string>
#include <sstream>
#include <map>

using namespace std;

map<string, string> parent;
vector<pair<string, string>> v;

string find(string name) {
	if (parent[name] == name) {
		return name;
	}
	else {
		parent[name] = find(parent[name]);
		return parent[name];
	}
}

void unite(string name1, string name2) {
	name1 = find(name1);
	name2 = find(name2);

	if (name1 == name2) return;
	parent[name2] = name1;
}

int main() {
	int n;
	cin >> n;
	getchar();
	vector<pair<string, string>> email_username;

	for (int i = 0; i < n; i++) {
		string str;
		getline(cin, str);
		istringstream is(str);
		vector<string> name;
		while (is >> str) {
			name.push_back(str);
		}
		parent[name[0]] = name[0];
		v.push_back(make_pair(name[0], name[0]));
		for (int i = 2; i < name.size(); i++) {
			email_username.push_back(make_pair(name[i], name[0]));
		}
	}

	map<string, string> visited_email_username;

	for (int i = 0; i < email_username.size(); i++) {
		map<string, string>::iterator it = visited_email_username.find(email_username[i].first);
		if (it != visited_email_username.end()) {
			unite(it->second, email_username[i].second);
		}
		else {
			visited_email_username[email_username[i].first] = email_username[i].second;
		}
	}

	vector<pair<string, vector<string>>> res;

	map<string, string>::iterator iter = parent.begin();

	while (iter != parent.end()) {
		iter->second = find(iter->first);
		iter++;
	}

	for (int i = 0; i < v.size(); i++) {
		v[i].second = parent[v[i].first];
		if (res.empty()) {
			res.push_back(make_pair(v[i].second, vector<string>(1, v[i].first)));
		}
		else {
			for (int j = 0; j < res.size(); j++) {
				if (res[j].first == v[i].second) {
					res[j].second.push_back(v[i].first);
					break;
				}
				else if (j == res.size() - 1) {
					res.push_back(make_pair(v[i].second, vector<string>(1, v[i].first)));
					break;
				}
			}
		}
	}



	vector<pair<string, vector<string>>>::iterator it = res.begin();
	while (it != res.end()) {
		vector<string> temp = it->second;
		string str = "";
		for (int i = 0; i < temp.size(); i++) {
			str += temp[i] + " ";
		}
		str.erase(str.find_last_not_of(" ") + 1);

		cout << str << endl;
		it++;
	}

	return 0;
}
```