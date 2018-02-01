---
layout:     post
title:      "动态规划"
subtitle:   "Dynamic Programming"
date:       2018-01-25 12:00:00
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

### 堆砖块

小易有n块砖块，每一块砖块有一个高度。小易希望利用这些砖块堆砌两座相同高度的塔。为了让问题简单，砖块堆砌就是简单的高度相加，某一块砖只能使用在一座塔中一次。小易现在让能够堆砌出来的两座塔的高度尽量高，小易能否完成呢。

**输入描述：**

输入包括两行： 第一行为整数n(1 ≤ n ≤ 50)，即一共有n块砖块 第二行为n个整数，表示每一块砖块的高度height[i] (1 ≤ height[i] ≤ 500000)

**输出描述：**

如果小易能堆砌出两座高度相同的塔，输出最高能拼凑的高度，如果不能则输出-1. 保证答案不大于500000。

**Example**

**Input**

```
3
2 3 5
```

**Output**

```
5
```

**AC Code**

```
#include <iostream>
#include <vector>
#include <algorithm>
#define MIN_INT -0x4f4f4f4f
 
using namespace std;
 
 
int main() {
    int n;
    cin >> n;
 
    vector<int> height(n + 1, 0);
 
    int sum = 0;
 
    for (int i = 1; i <= n; i++) {
        cin >> height[i];
        sum += height[i];
    }
 
    vector<vector<int>> dp(2, vector<int>(sum + 1, MIN_INT));
 
    dp[0][0] = 0;
 
    for (int i = 1; i <= n; i++) {
        int cnt_index = i % 2;
        int bef_index = (i + 1) % 2;
        int brick_height = height[i];
        for (int j = 0; j <= sum; j++) {
            dp[cnt_index][j] = dp[bef_index][j];
            if (j + brick_height <= sum) {
                dp[cnt_index][j] = max(dp[cnt_index][j], dp[bef_index][j + brick_height] + brick_height);
            }
            if (brick_height - j >= 0) {
                dp[cnt_index][j] = max(dp[cnt_index][j], dp[bef_index][brick_height - j] + brick_height - j);
            }
            if (j - brick_height >= 0){
                dp[cnt_index][j] = max(dp[cnt_index][j], dp[bef_index][j - brick_height]);
            }
        }
    }
 
    cout << (dp[n % 2][0] > 0 ? dp[n % 2][0] : -1) << endl;
 
    return 0;
 
}
```

**解题思路**

> 转自[http://blog.csdn.net/zhufenghao/article/details/69802667](http://blog.csdn.net/zhufenghao/article/details/69802667)

i表示使用了前i块砖，j表示两砖块的高度差，dp[i][j]表示使用了前i块砖较矮的一堆砖的高度，则状态转移如下：

$$dp[i][j] = \left\{\begin{matrix} 0 & i = 0, j = 0 \\ -\infty & i = 0, j \neq 0\\ max\begin{pmatrix} dp[i-1][j] & (1) \\ dp[i-1][j + h_{i}] + h_{i} & (2)\\ dp[i-1][h_{i} - j] + h_{i} - j & (3)\\ dp[i-1][j - h_{i}] & (4)\end{pmatrix} & i > 0 \end{matrix}\right.$$

(1) 不放置第i块砖块
(2) 放置第i块砖块到矮堆，矮堆依然是矮堆
(3) 放置第i块砖块到矮堆，矮堆变成高堆
(4) 放置第i块砖块在高堆

![](/img/dynamic-programming/brick.png)