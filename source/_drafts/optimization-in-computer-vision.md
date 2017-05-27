---
title: optimization in computer vision
mathjax: true
tags:
---


用优化准则(optimizatioin criterion)来构建（formulate）所需的变换（desired transformation），从而找到所需的最优解。


两种不同的方法(two different variants)

## 变分方法 regularization (variational methods) 
构建连续的全局能量方程(continuous global energy function)，用线性方法或者迭代技术进行。

try to recovery unknown function $f(x, y)$ from the data points sampled. 这个问题又称为反问题(inverse problem)

1. 一阶微分的平方的积分 
$$\mathcal{E}_1=\displaystyle\int f_x^2(x) dx$$
2. 二阶微分的平方的积分

可以看出能量将函数$f$映射为一个标量，事实上，能量的度量是一种算子（functional）。

1. 一阶微分范数（first derivative norm) 通常称为膜(membrance)
2. second derivative norm 成为 薄板样条(thin-plate spline)

实践里
1. 图像一般使用一阶平滑项(first order smoothness term)


1. data penalty
    $$\mathcal{E}_d$$
2. smoothness penalty
    $$\mathcal{E}_s$$
    一阶正则化可解释为电阻网(resistive grid)，输入 是$d(i,j)$，$s_x(i,j)$，$s_y(i,j)$是导电系数。梯度越小电阻越小， 梯度越大电阻越大。
    ![一阶正则图](http://oor53bfqy.bkt.clouddn.com/first-order-regularization-graph.png?imageView2/2/w/350/q/100)
优化问题
$$\mathcal{E} = \mathcal{E}_d + \mathcal{E}_s$$ 连续问题的最小化，

## 贝叶斯统计方法 Bayesian statistics
1. 对测量过程（measurement process）建模
2. 对解空间（solution space) 建模


