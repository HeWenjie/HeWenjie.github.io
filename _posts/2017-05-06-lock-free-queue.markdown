---
layout:     post
title:      "无锁队列"
subtitle:   "lock-free queue"
date:       2017-05-06 12:00:00
author:     "HE"
header-img: "img/home-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 多线程
---

### 概述

队列是一种特殊的线性表，其一个重要的特征就是FIFO，只能在表的后端进行插入操作，在表的前端进行删除操作。FIFO符合流水线业务流程，在进程间通信、网络通信中也经常使用队列作为缓存，缓解数据处理压力。

为了提高应用的性能，一个主要方式就是采用并行编程，其中主要是多线程编程。在操作系统中，进程是系统资源分配的最小单位，而线程是系统调度的最小单位。无论线程和进程，都会遇到通信的问题，有两种方式处理这个问题：简单的方式、复杂的方式。

### 简单的方式

简单的方式就是教科书般的解决办法：用锁机制来对多个线程之间共享的临界区进行保护。

互斥锁，在同一时间只能有一个线程/进程访问临界区资源。任何线程在进入临界区之前，必须要先获取临界区的互斥锁，如果已经有另一个线程拥有了临界区的互斥锁，则当前线程必须等待，直到拥有互斥锁的线程释放互斥锁。

```c++
// 互斥锁队列代码
```

### 复杂的方式

无锁编程！无锁队列的实现是基于CPU提供的一种原子操作CAS。

##### CAS原子操作

原子操作，就是指不会被线程调度机制打断的操作，原子操作一旦开始，就一直运行到结束，中间不会有任何上下文切换。

CAS，Compare and Swap，比较并交换，CAS通过将内存中的值与指定数据进行比较，当数值一样时将内存中的数据替换为新的值。在Intel处理器中，CAS操作通过指令CMPXCHG实现，CAS原子操作在维基百科中的代码如下所示：

```c++
int compare_and_swap(int* reg, int oldval, int newval)
{
    ATOMIC();
	int old_reg_val = *reg;
	if (old_reg_val == oldval)
		*reg = newval;
	END_ATOMIC();
	return old_reg_val;
}
```

##### 无锁队列的实现

下面的无锁队列的实现来自John D. Valois的[Implementing Lock-Free Queues](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.53.8674&rep=rep1&type=pdf)

进队列的CAS实现方式：

````c++
// 根据论文中的伪代码实现
void ENQUEUE(x) {
	q = new record();
	q->value = x;
	q->next = NULL;
	do {
		p = tail;
		succ = CAS(p, next, )
	} while ();
}
```