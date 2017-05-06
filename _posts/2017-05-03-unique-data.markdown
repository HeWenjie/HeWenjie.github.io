---
layout:     post
title:      "数组中不重复的数字"
subtitle:   ""
date:       2017-05-03 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - C++
    - 算法
---

## 问题描述

给出$$k*n+m$$个的数字，除其中$$m$$个数字之外其他每个数字均出现$$k$$次，找到这个数字

## 解决方法

#### 最基本问题

考虑$$k=2,m=1$$时，即给出$$2*n+1$$个的数字，除其中一个数字之外其他每个数字均出现两次。解决这个问题的方法非常简单，利用一个数与自己异或的结果为$$0$$，不难发现，由于除一个数字之外其他数字都只出现了两次，这样这些数字异或的结果为$$0$$，而只出现一次的数字和$$0$$异或会得到本身，也就是说，只要把所有数字进行异或，就可以得到只出现一次的数字，其C++程序如下：

```c++
int singleNumber(vector<int> &A) {
	int sinNum = 0;
	for (int i = 0; i < A.size(); i++) {
		sinNum ^= A[i];
	}
	return sinNum;
}
```

#### 基本问题的变种

* 重复次数是三次或以上

  当$$k\geqslant 3,m=1$$时，即给出$$k*n+1(k\geqslant 3)$$个的数字，除其中一个数字之外其他每个数字均出现三次或以上。将每个数字转换成二进制，不难发现，将数组中的每一个数的二进制位相加，因为其他数字都出现了三次，所以相加的每一位除以$$3$$，其余数必定为$$0$$。而由于再加上了只出现一次的数字，则导致相应二进制位除以$$3$$的余数不为$$0$$。例子如下，取模之后结果为只出现一次的数字。

  ![Frame1](/img/uni-num/1.png)

  其C++程序如下：（该解法适用于重复次数为三次或以上，而不重复数字只有一个的情况）

  ```c+
  int singleNumber(vector<int> &A) {
      const int intBits = sizeof(int) * 8; // 求出int的位数
      int *binArray = new int[intBits]{0};
      // 按二进制位求和
      for (int i = 0; i < A.size(); i++) {
          for (int j = 0; j < intBits; j++) {
              binArray[j] += ((A[i] >> j) & 1);
          }
      }
      int sinNum = 0;
      for (int i = 0; i < intBits; i++) {
          if (binArray[i] % 3 != 0) {
              sinNum += (1 << i);
          }
      }

      delete binArray;
      return sinNum;
  }
  ```


* 非重复的数字个数是两个

  当$$k=3,m=1$$时，即给出$$2*n+2$$个的数字，除其中两个数字之外其他每个数字均出现两次。与基本问题有着同样的思路，除了两个不重复的数字，其他数字都出现了两次，而这些数字异或之后的结果为$$0$$，而由于两个不重复的数字不完全相同，这两个数字异或之后，必定至少有一位为1，然后判断数组中该位是否为$$1$$分为两组，其中一组为该位为$$0$$，而另一组该位为$$1$$，就能将两个不重复的数字分别分到两组中，这样对于任意一组，问题就退化为最基本问题，分别利用最基本问题的解法，就能分别求出非重复数字。其C++程序如下所示：

  ```c++
  vector<int> singleNumber(vector<int> &A) {
      int xor_result = 0; // 数组中所有数异或的结果
      for (int i = 0; i < A.size(); i++) {
          xor_result ^= A[i];
      }
      // 求出第一位异或结果为1的下标
      int index = 0;
      int index_value = 1; // 该下标对应的二进制的值

      while (xor_result >= index_value) {
          index_value *= 2;
          index++;
      }
      index--;

      vector<int> zero; // 该位为0的数字的集合
      vector<int> one;  // 该位为1的数字的集合

      for (int i = 0; i < A.size(); i++) {
          if (((A[i] >> index) & 1) == 0) {
              zero.push_back(A[i]);
          } else {
              zero.push_back(A[i]);
          }
      }

      int zero_result = 0; // 0集合异或结果
      int one_result = 0;  // 1集合异或结果

      for (int i = 0; i < zero.size(); i++) {
          zero_result ^= zero[i];
      }
      for (int i = 0; i < one.size(); i++) {
          one_result ^= one[i];
      }
      vector<int> sinNum;
      sinNum.push_back(zero_result);
      sinNum.push_back(one_result);
      return sinNum;
  }
  ```