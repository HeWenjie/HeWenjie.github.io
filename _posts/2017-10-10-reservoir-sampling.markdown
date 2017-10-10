---
layout:     post
title:      "蓄水池算法"
subtitle:   ""
date:       2017-10-10 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 算法
    - LeetCode
---

### 问题描述

从$$N$$个元素中随机抽取$$K$$个元素，使得每个元素被抽取的概率相同

### 问题分析

先把问题简化成：从$$N$$个元素中抽取$$1$$个元素，使得每个元素被抽取的概率都是$$\frac{1}{N}$$

当$$N$$是已知时，则问题非常简单，利用$$rand()$$函数随机生成$$0~N-1$$中的某个数$$m$$，直接选取下标为$$m$$的元素。

但是当$$N$$是未知时，该问题就可以抽象为蓄水池抽样问题，即从一个包含$$N$$个对象的集合中随机选取$$K$$个对象，$$N$$是一个非常大或者不知道的值。一个实际的例子就是大数据的处理，当数据大到无法放到内存中时，就需要使用蓄水池算法来随机抽样。而现在这个问题就是蓄水池问题的特例，即$$K=1$$。

这个问题的解法就是总是选取第$$1$$个元素，以$$\frac{1}{2}$$的概率选取第$$2$$个元素，以$$\frac{1}{3}$$的概率选取第$$3$$个元素，以此类推，以$$\frac{1}{m}$$的概率选取第$$m$$个元素。则每一个对象都具有相同的选中概率，也就是$$\frac{1}{n}$$。

不难理解，第$$m$$个元素被选中的概率$$=$$选择第$$m$$个元素的概率$$\times$$第$$m$$个元素之后的元素不被选择的概率，有

$$p(m)=\frac{1}{m}\times \left ( \frac{m}{m+1}\times \frac{m+1}{m+2}\times ... \times \frac{n-1}{n}\right )=\frac{1}{n}$$

**（严谨的证明在下面章节）**

*《The Art of Computer Programming》*的伪代码：

```c++
Init : a reservoir with the size: k
for i = k + 1 to N
    M = random(1, i)
    if (M < k)
        SWAP the Mth value and ith value
end for
```

### 蓄水池算法证明

下面通过数学归纳法证明蓄水池算法的正确性

蓄水池算法：前$$K$$个元素放入蓄水池中，从$$i=k+1$$开始以$$\frac{k}{i}$$的概率选取第$$i$$个元素，并以均等的概率替换蓄水池中的某个元素。则能从$$N$$个元素的集合中以均等的概率选取$$K$$个元素。

**证明如下：**

当$$i=k+1$$时，则选取第$$k+1$$个元素的概率是$$\frac{k}{k+1}$$，前面k个元素被选取的概率$$=1-$$第$$k+1$$个元素被放入蓄水池的概率$$=1-\frac{k}{k+1}\times \frac{1}{k}=\frac{k}{k+1}$$，结论成立

假设当$$j=i$$的时候结论成立，则选择第$$i$$个元素的概率是$$\frac{k}{i}$$，前$$i-1$$个元素出现在蓄水池的概率都为$$\frac{k}{i}$$，即要证明$$j=i+1$$时，以$$\frac{k}{i+1}$$的概率选取第$$i+1$$个元素，前$$i$$个任意一个元素出现在蓄水池中的概率都为$$\frac{k}{i+1}$$

第$$i+1$$个元素被选择的概率是$$\frac{k}{i+1}$$，蓄水池中任一元素被选择替换的概率是$$\frac{1}{k}$$，则蓄水池中元素被第$$i+1$$个元素替换的概率是$$\frac{k}{i+1} \times \frac{1}{k}=\frac{1}{i+1}$$

所以蓄水池中元素没有被替换的概率是$$1-\frac{1}{i+1}=\frac{i}{i+1}$$，又因为在选择第$$i+1$$个元素前$$i$$个任一元素出现在蓄水池中的概率为$$\frac{k}{i}$$，所以在选择第$$i+1$$个元素后前$$i$$个任一元素出现在蓄水池中的概率为$$\frac{k}{i} \times \frac{i}{i+1}=\frac{k}{i+1}$$，结论成立

### 蓄水池算法代码

```c++
// 已知N的大小，但是N非常大（N >> K）
void reservoir_sampling(vector<int> samples, vector<int> result, int N, int K) {

    srand(time(NULL))
    if (samples.empty()) return;

    result.resize(K);

    // 选择前K个元素放入蓄水池
    for (int i = 0; i < K; i++) {
        result[i] = samples[i];
    }

    // 以概率K/i选取第i个元素
    for (int i = K; i < N; i++) {
        int j = rand() % (i + 1);
        if (j < K) {
            result[j] = samples[i];
        }
    }
}
```

```c++
// 不知道N的大小，假设N不会小于K
void reservoir_sampling(vector<int> samples, vector<int> result, int K) {

    srand(time(NULL))
    if (samples.empty()) return;

    result.resize(K);

    vector<int>::iterator iter = samples.begin();
    // 选择前K个元素放入蓄水池
    for (int i = 0; i < K; i++) {
        result[i] = *iter++;
    }

    // 以概率K/i选取第i个元素
    for (int i = m; iter != samples.end(); i++, iter++) {
        int j = rand() % (i + 1);
        if (j < K) {
            result[j] = *iter;
        }
    }
}
```

### LeetCode 382

这道问题就可以用蓄水池算法解决

问题描述：Given a singly linked list, return a random node's value from the linked list. Each node must have the same probability of being chosen.

Follow up: What if the linked list is extremely large and its length is unknown to you? Could you solve this efficiently without using extra space?

Example:
```c++
// Init a singly linked list [1,2,3].
ListNode head = new ListNode(1);
head.next = new ListNode(2);
head.next.next = new ListNode(3);
Solution solution = new Solution(head);

// getRandom() should return either 1, 2, or 3 randomly. Each element should have equal probability of returning.
solution.getRandom();
```

**AC**代码如下：

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    /** @param head The linked list's head.
        Note that the head is guaranteed to be not null, so it contains at least one node. */
    Solution(ListNode* head) {
        this->head = head;
    }
    
    /** Returns a random node's value. */
    int getRandom() {
        int res = head->val;
        ListNode* node = head->next;
        for (int i = 1; node != NULL; i++) {
            int j = rand() % (i + 1);
            if (j == 0) {
                res = node->val;
            }
            node = node->next;
        }
        return res;
    }
private:
    ListNode* head;
};

/**
 * Your Solution object will be instantiated and called as such:
 * Solution obj = new Solution(head);
 * int param_1 = obj.getRandom();
 */
```