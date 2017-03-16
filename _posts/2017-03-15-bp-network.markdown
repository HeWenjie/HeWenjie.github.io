---
layout:     post
title:      "BP神经网络"
subtitle:   "BP Network"
date:       2017-03-14 12:00:00
author:     "HE"
header-img: "img/bp-bg.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 机器学习
    - 神经网络
---

## BP神经网络概述

BP神经网络是一种按误差逆传播算法训练的多层前馈网络，其学习规则是使用梯度下降法，通过反向传播来不断调整网络的权值和偏置值，使得网络预测值和实际值的误差平方和最小。在BP神经网络中，单个样本有$$m$$个输入，$$n$$个输出，在输入层和输出层之间还有若干个隐藏层。

BP神经网络分为以下两个过程：
1. 输入信号正向传播
2. 误差信号反向传播

在正向传播过程中，输入信号从输入层经隐藏层逐层处理，直至输出层。每一层的神经网络状态只影响下一层的神经元的状态。如果输出层的预测输出与实际输出之间误差较大，则进入反向传播，各层根据预测误差调整网络的权值和阀值，从而使得BP神经网络的预测输出不断逼近实际输出。

一个BP神经网络包括输入层，隐藏层以及输出层。如下图所示：

![BP Network](/img/bp-network.jpg)

## BP神经网络原理

#### 正向传播过程

BP神经网络的正向传播就是输入信号从输入层经隐藏层处理最后从输出层得到输出信号。每一层包含若干个神经元，神经元$$i$$的输出为$$x_{i}$$，神经元$$i$$的偏置值为$$b_{i}$$，神经元$$i$$和神经元$$j$$之间的权值为$$w_{ij}$$，计算公式如下：

$$s_{j}=\sum_{i=0}^{m-1}w_{ij}x_{i}+b_{j}$$

$$x_{j}=f(s_{j})$$

BP神经网络权值和偏置值的更新是使用梯度下降法，这就要求激活函数可导，所以BP神经网络的激活函数一般为S型函数或者线性函数。
BP神经网络的输入层没有偏置值。

#### 反向传播过程

BP神经网络的反向传播就是根据预测值和真实值的误差来反向调整各层的权值和偏置值。假设输出层的预测结果为$$y_{j}$$，而真实结果是$$\hat{y_{j}}$$，输出层误差函数如下：

$$E(w,b)=\frac{1}{2}(Y-\hat{Y})=\frac{1}{2}\sum_{j=0}^{n-1}(y_{j}-\hat{y_{j}})^{2}$$

将误差函数推广到隐藏层：

$$E(w,b)=\frac{1}{2}\sum_{j=0}^{n-1}(f(\sum_{i=0}^{h-1}w_{ij}x_{i}+b_{j})-\hat{y_{j}})^{2}$$

同理，将误差函数推广到输入层：

$$E(w,b)=\frac{1}{2}\sum_{j=0}^{n-1}(f(\sum_{i=0}^{h-1}w_{ij}f(\sum_{k=0}^{m-1}w_{ki}x_{k}+b_{i})+b_{j})-\hat{y_{j}})^{2}$$

BP神经网络的学习规则是使用梯度下降法，通过反向传播来不断调整网络的权值和偏置值，使得网络预测值和实际值的误差平方和最小。梯度下降发的计算过程就是沿着梯度下降的方向求解极小值。

神经元$$i$$和神经元$$j$$之间的权值$$w_{ij}$$迭代公式如下：

$$w_{ij}=w_{ij}-\eta \frac{\partial E(w,b)}{\partial w_{ij}}$$

同理，神经元$$i$$的偏置值$$b_{i}$$迭代公式如下：

$$b_{i}=b_{i}-\eta \frac{\partial E(w,b)}{\partial b_{i}}$$

其中$$\frac{\partial E(w,b)}{\partial w_{ij}}$$和$$\frac{\partial E(w,b)}{\partial b_{i}}$$就要求激活函数可导。

根据梯度下降学习规则，关键是要求解误差函数关于各个权值的梯度，隐藏层到输出层的权值梯度如下：

$$\Delta w_{ij}=-\eta \frac{\partial E(w,b)}{\partial w_{ij}}=-\eta \frac{\partial E(w,b)}{\partial x_{j}}\cdot \frac{\partial x_{j}}{\partial s_{j}}\cdot \frac{\partial s_{j}}{\partial w_{ij}}$$

这时的误差函数为输出层的误差函数，所以：

$$\Delta w_{ij}=-\eta (y_{j}-\hat{y_{j}})\cdot f'(s_{j})\cdot x_{i}$$

根据Delta学习规则，设$$\delta_{0}=(y_{j}-\hat{y_{j}})\cdot f'(s_{j})$$，则$$\Delta w_{ij}=-\eta\cdot \delta_{0}\cdot x_{i}$$

输入层到隐藏层的权值梯度如下：

$$\Delta w_{ki}=-\eta \frac{\partial E(w,b)}{\partial w_{ki}}=-\eta \frac{\partial E(w,b)}{\partial x_{i}}\cdot \frac{\partial x_{i}}{\partial s_{i}}\cdot \frac{\partial s_{i}}{\partial w_{ki}}$$

这时的误差函数为隐藏层的误差函数，所以：

$$\Delta w_{ki}=-\eta \sum_{j=0}^{n-1}(y_{j}-\hat{y_{j}})\cdot f'(s_{j})\cdot w_{ij}\cdot f'(s_{i})\cdot x_{k}=-\eta \sum_{j=0}^{n-1}\delta_{0}\cdot w_{ij}\cdot f'(s_{i})\cdot x_{k}$$

设$$\delta_{1}=\sum_{j=0}^{n-1}\delta_{0}\cdot w_{ij}\cdot f'(s_{i})$$，则$$\Delta w_{ki}=-\eta \cdot \delta_{1}\cdot x_{k}$$

现假设选取以下S型函数作为激活函数，其形式如下：

$$f(x)=\frac{A}{1+e^{-\frac{x}{B}}}$$

则该S型函数的导数如下：

$$f'(x)=\frac{-A\cdot e^{-\frac{x}{B}}\cdot -\frac{x}{B}}{(1+e^{-\frac{x}{B}})^{2}}=\frac{x}{AB}\cdot \frac{A}{1+e^{-\frac{x}{B}}}\cdot (A-\frac{A}{1+e^{-\frac{x}{B}}})=\frac{xf(x)(A-f(x))}{AB}$$

将该S型函数的导数带入到权值梯度式子中，则对于隐藏层神经元$$i$$和输出层神经元$$j$$之间的权值梯度为：

$$\Delta w_{ij}=-\eta (y_{j}-\hat{y_{j}})\cdot \frac{s_{j}\cdot x_{j}(A-x_{j})}{AB}\cdot x_{i}$$

同理，对于输入层神经元$$k$$和隐藏层神经元$$i$$之间的权值梯度为：

$$\Delta w_{ki}=-\eta \sum_{j=0}^{n-1}\delta_{0}\cdot w_{ij}\cdot \frac{s_{i}\cdot x_{i}(A-x_{i})}{AB}\cdot x_{k}$$

对于输出层神经元$$j$$的偏置值的梯度为：

$$\Delta b_{j}=-\eta (y_{j}-\hat{y_{j}})\cdot \frac{s_{j}\cdot x_{j}(A-x_{j})}{AB}$$

对于隐藏层神经元$$i$$的偏置值的梯度为：

$$\Delta b_{i}=-\eta \sum_{j=0}^{n-1}\delta_{0}\cdot w_{ij}\cdot \frac{s_{i}\cdot x_{i}(A-x_{i})}{AB}$$

## BP神经网络实现（Python）

BP神经网络的python实现如下所示，其中激活函数使用了sigmoid函数，只包含一层隐藏层，隐藏层的神经元数目由$$h=\sqrt{m+n}+\alpha$$来决定。

```python
# -- coding:utf-8 --
import numpy as np
import math

MAX_NUM = 10		# 每一层的最多神经元数

A = 10.0			# sigmoid函数参数A
B = 10.0			# sigmoid函数参数B
ITERS = 1000		# 最大的训练次数
ETA_W = 0.0035		# 权值的学习率
ETA_B = 0.001		# 偏置值的学习率
ALPHA = 5			# 隐藏层神经元调节常数
ERROR = 0.001		# 单个样本允许的误差
ACCU = 0.001		# 每个迭代允许的误差

X_SIZE = 3			# 输入层神经元数目
Y_SIZE = 1			# 输出层神经元数目

class BP:
	in_num = X_SIZE			# 输入层神经元数目
	hd_num = MAX_NUM		# 隐藏层神经元数目
	out_num = Y_SIZE		# 输出层神经元数目
	x = []			# 输入数据
	y = []			# 输出数据
	w = []			# BP网络权值
	b = []			# BP网络节点阀值
	s = []			# 每个神经元经过激活函数之后的输出值
	d = []			# 存储delta学习规则delta的值

	def __init__(self):									# 初始化BP网络
		self.get_nodes()
		w_ih = np.zeros((self.in_num, self.hd_num))		# 输入层到隐藏层的权值
		w_ho = np.zeros((self.hd_num, self.out_num))	# 隐藏层到输出层的权值
		self.w.append(w_ih)
		self.w.append(w_ho)
		b_h = np.zeros((self.hd_num, 1))				# 隐藏层节点的偏置值
		b_o = np.zeros((self.out_num, 1))				# 输出层节点的偏置值
		self.b.append(b_h)
		self.b.append(b_o)
		s_i = np.zeros((self.in_num, 1))				# 输入层神经元
		s_h = np.zeros((self.hd_num, 1))				# 隐藏层神经元
		s_o = np.zeros((self.out_num, 1))				# 输出层神经元
		self.s.append(s_i)
		self.s.append(s_h)
		self.s.append(s_o)
		d_h = np.zeros((self.hd_num, 1))				# 隐藏层神经元的delta值
		d_o = np.zeros((self.out_num, 1))				# 输出层神经元的delta值
		self.d.append(d_h)
		self.d.append(d_o)

	def get_data(self, array):							# 获取训练集
		for line in array:
			self.x.append(np.array([line[:-Y_SIZE]]))
			self.y.append(np.array([line[-Y_SIZE:]]))

	def get_nodes(self):								# 获取隐藏层神经元数目
		self.hd_num = int(math.sqrt(self.in_num + self.out_num)) + ALPHA
		if self.hd_num > MAX_NUM:						# 隐藏层神经元数目不能超过MAX_NUM
			self.hd_num = MAX_NUM

	def sigmoid(self, x):
		return A / (1 + math.exp(-x / B))

	def forward_propagation(self):
		# 计算隐藏层各神经元输出值
		temp = np.dot(np.transpose(self.w[0]), self.s[0]) + self.b[0]
		for i in range(0, self.hd_num):
			self.s[1][i] = self.sigmoid(temp[i][0])

		# 计算输出层各神经元输出值
		temp = np.dot(np.transpose(self.w[1]), self.s[1]) + self.b[1]
		for i in range(0, self.out_num):
			self.s[2][i] = self.sigmoid(temp[i][0])

	def back_propagation(self, cnt):
		self.calculate_delta(cnt)
		self.update_network()

	def calculate_delta(self, cnt):						# 计算delta学习规则的delta值
		# 计算输出层的delta值
		self.d[1] = (self.s[2] - np.transpose(self.y[cnt])) * self.s[2] * (A - self.s[2]) / (A * B)

		# 计算隐藏层的delta值
		self.d[0] = np.dot(self.w[1], self.d[1]) * self.s[1] * (A - self.s[1]) / (A * B)

	def update_network(self):							# 更新神经网络
		# 更新隐藏层到输出层的权值
		self.w[1] -= ETA_W * np.dot(self.s[1], np.transpose(self.d[1]))

		# 更新隐藏层到输出层的偏置值
		self.b[1] -= ETA_B * self.d[1]

		# 更新输入层到隐藏层的权值
		self.w[0] -= ETA_W * np.dot(self.s[0], np.transpose(self.d[0]))

		# 更新输入层到隐藏层的偏置值
		self.b[0] -= ETA_B * self.d[0]

	def get_error(self, cnt):
		temp = self.s[2] - self.y[cnt]
		return 0.5 * np.dot(np.transpose(temp), temp)

	def get_accu(self):
		accu = 0
		for cnt in range(0, len(self.x)):
			self.s[0] = np.transpose(self.x[cnt])
			self.forward_propagation()
			temp = self.s[2] - self.y[cnt]
			accu += 0.5 * np.dot(np.transpose(temp), temp)
		return accu[0][0]


	def train(self):
		print "Begin"
		for i in range(0, ITERS):
			for cnt in range(0, len(self.x)):
				self.s[0] = np.transpose(self.x[cnt])
				while 1:
					self.forward_propagation()
					if self.get_error(cnt) < ERROR:
						break
					self.back_propagation(cnt)
			print "Iteration times: " + str(i)
			accu = self.get_accu()
			print "Accuracy: " + str(accu)
			if accu < ACCU:
				break
		print "End"

	def forcast(self, x):
		self.s[0] = x
		self.forward_propagation()
		return self.s[2]

train_set = [[5, 5, 5, 10]]

bp = BP()
bp.get_data(train_set)
bp.train()
while 1:
	a = raw_input()
	b = raw_input()
	c = raw_input()
	x = np.array([[int(a)], [int(b)], [int(c)]])
	print bp.forcast(x)
```

## 知识补充

#### 隐藏层的神经元数

在BP神经网络中，输入层神经元的个数由样本特征的维度决定，输出层神经元的个数由样本的类别个数决定。而隐藏层的层数和每层的神经元个数由用户指定。对于不同的问题，隐藏层神经元数目的选取也不一样，同时隐藏层神经元的数目还影响神经网络的性能。一般隐藏层神经元数目的选取有如下经验公式：

$$h=\sqrt{m+n}+\alpha$$

$$h=log_{2}m$$

$$h=\sqrt{mn}$$

其中，h是隐藏层的神经元数目，m是输入层的神经元数目，n是输出层的神经元数目，$$\alpha$$是1~10的调节常数

#### Delta学习规则
Delta学习规则是一种简单的监督学习算法，该算法根据神经元输出的预测值与真实值的差别来调整权值，其表达式如下：

$$w_{ij}(t+1)=w_{ij}(t)+\alpha (\hat{y_{j}}-y_{j})x_{i}(t)$$

$$\hat{y_{j}}$$是神经元的真实值，$$y_{j}$$是神经元的预测值。由此，可知Delta学习规则就是：如果神经元预测值比真实值大，则减小所有输入为正的连接的权重，增大所有输入为负的连接的权重；反之，如果神经元预测值比真实值小，则增大所有输入为正的连接的权重，减小所有输入为负的连接的权重。