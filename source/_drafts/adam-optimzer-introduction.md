---
title: adam optimizer introduction
tags:
---



Adam 是 SGD 的一个扩展(extension)。

## 什么是 Adam 优化算法？
> the name Adam is derived from adaptive moment estimation.

它有很多优点... 

> Stochastic gradient descent maintains a single learning rate (termed alpha) for all weight updates and the learning rate does not change during training.

随机梯度下降只有一个学习率(learning rate)参数, 该参数在整个训练的过程中是不变的。

Adam 为每一组参数 计算 独自的 学习率。它兼具以下两种优化算子：
1. Adaptive Gradient Algorithm (AdaGrad) 
2. Root Mean Square Propagation (RMSProp)



## reference
1. https://machinelearningmastery.com/adam-optimization-algorithm-for-deep-learning/
