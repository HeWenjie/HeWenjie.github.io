---
layout:     post
title:      "动态规划"
subtitle:   "Dynamic Programming"
date:       2018-02-01 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - C++
    - 算法
---

### 小易喜欢的数列

小易非常喜欢拥有以下性质的数列：

1. 数列的长度为n

2. 数列中的每个数都在1到k之间(包括1和k)

3. 对于位置相邻的两个数A和B(A在B前)，都满足(A <= B)或(A mod B != 0)(满足其一即可)

例如，当n = 4， k = 7，那么{1,7,7,2}，它的长度是4，所有数字也在1到7范围内，并且满足第三条性质，所以小易是喜欢这个数列的。但是小易不喜欢{4,4,4,2}这个数列。小易给出n和k，希望你能帮他求出有多少个是他会喜欢的数列。

**输入描述：**

输入包括两个整数n和k(1 ≤ n ≤ 10, 1 ≤ k ≤ 10^5)

**输出描述：**

输出一个整数,即满足要求的数列个数,因为答案可能很大,输出对1,000,000,007取模的结果。

**Example**

**Input**

```
2 2
```

**Output**

```
3
```

**AC Code**

```
#include <iostream>
#include <vector>

using namespace std;

static int num = 1000000007;

int main() {
	int n;
	int k;
	cin >> n >> k;

	vector<vector<int>> dp(n + 1, vector<int>(k + 1, 0));

	for (int i = 1; i <= k; i++) {
		dp[1][i] = 1;
		dp[1][0] += dp[1][i];
	}

	for (int i = 2; i <= n; i++) {
		int sum = 0;
		for (int j = 1; j <= k; j++) {
			sum += dp[i - 1][j];
			sum %= num;
		}

		for (int j = 1; j <= k; j++) {
			int sum2 = 0;
			for (int l = 2 * j; l <= k; l += j) {
				sum2 += dp[i - 1][l];
				sum2 %= num;
			}
			dp[i][j] = (sum - sum2) % num;
		}
	}

	int result = 0;

	for (int i = 1; i <= k; i++) {
		result += dp[n][i];
		result %= num;
	}

	cout << result << endl;
	return 0;

}
```

**解题思路**

i表示数组长度为i，j表示数组最后一个元素是j，dp[i][j]是长度为i的且数组最后一个元素是j的满足题目要求的数列的数量。

则有$$1 \leqslant i \leqslant n, 1 \leqslant j \leqslant k$$，则状态转移方程如下：

$$dp[i][j] = \sum_{x=1}^{k}dp[i-1][x]-\sum_{y=1}^{k}dp[i-1][y](y > j 且 y \ mod\  j == 0)$$