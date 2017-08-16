---
title: dynamic programming tutorial
tags:
---


# 动态规划 从新手到老司机

一类重要的问题可以用动态规划进行求解。
能处理这类问题可以让你的功力大增。
原始的理论很难懂。

## 介绍
> 什么是动态规划，如何描述它？

>> DP is not an algorithm. It's a techique/approach that we use to build efficient algorithms for problems of very specific class

动态规划是一种算法技术。
这种技术使用的是递归公式(recurrent formula)和一个初始状态。

问题的分支解基于前一步的解。
它的复杂度是多项式的。
比其他例如，回溯法和穷举法， 效率更高。

One can think of dynamic programming as a table-filling algorithm: you know the calculations you have to do, so you pick the best order to do them in and ignore the ones you don't have to fill in.
动态规划被认为是一个表格填充算法。

通过例子来理解：

> 给定$N$个硬币列，它的面值分别是$(V_1, V_2, \dots, V_N)$，那么，总面值为$S$的最小硬币数是多少？


现在我们开始构建动态规划的解决方案。

首先，我们需要找到一个已经具有最优解的状态(state), 同时也为找到下一个状态(next state)的最优解做好准备。

> 状态(state)代表什么？

他描述一种情况(situation), 是子问题的解。例如，它是和为$I$的解，其中$I \leq S$。
如果找到状态面值和为$\leq I$的的解，我们能轻松找到下一个状态$I+1$的解。

> 如何找到？

很简单，对每一个硬币$j, V_j \leq I$， 状态$I - V_j$的解为$m_j$，那$\min_j\lbrace m_j+1\rbrace$是最优的解。

这种方法和数学中的数学归纳法非常的像
1. 从 i = 0 开始
2. 再计算 i+1
3. 需要保存 从 0 到 i 的所有结果

## 基础

目前，我们讨论了非常简单的例子。
对困难的问题，我们需要寻找一个方法，发现如何从一个状态转变到另外一个状态。
我们引进一个术语（recurrent relation）迭代关系。


## 分治法的区别
> 分治法：a top-down approach

许多小的instance被多次计算。 

> 动态规划：a bottom-up approach

更小的instance被存储，用在后面的环节。


动态规划问题主要有两类：
1. 优化问题 Optimization Problem
    希望可以找到一个可行解feasible solution, 可以最大化或者最小化目标函数。
2. 组合问题 Combinatorial Problem
    希望知道做一件事有多少途径，或者，事件发生的概率

动态规划方法有下列模式：
1. 证明问题可以分解成子问题。
2. 使用更小的解循环定义解的值。
3. 从bottom到up求解问题。
4. 

## reference
1. https://www.hackerearth.com/practice/algorithms/dynamic-programming/introduction-to-dynamic-programming-1/tutorial/
2. 


