---
title: seperate graph into training and inference
tags:
---

深度网应当分成训练和推断两种。这在实践中已经遇到这个问题。如

[debug with tensorboard](https://kingease.github.io/2017/06/05/debug-with-tensorboard/)


## 分析
在训练时创建一个`graph`，在推断时又是另外一个`graph`，但是这两个`graph`又具有相同的部分。
因为，训练出来的权重应当在推断时使用。一般是在训练时，保存一份权重数据，也就是训练出来的*模型*。
1. 训练
2. 推断

曾经在[deep net structures](https://kingease.github.io/2017/05/24/deep-net-structures/)总结过，`graph`在两个阶段出现:
1. 创建阶段
2. Session的使用阶段

所以, Graph出现下面的情况:

|           | build | session |
|-----------|:-----:|:-------:|
| training  |   1   |    2    |
| inference |   3   |    4    |




## 如何使用

最好的使用方式，是建立Graph对象。
1. build的时候，根据is_training来区别，来建立不同的图。
2. graph对象还关联一系列Layers
    1. Inputs Layers
    2. Evaluate Layers
        3. cost
        4. prediction
        5. accuracy 
    3. Optimizer Layers
    4. tf.train.Saver

### 1. build for training

### 2. run for training

### 3. build for inference

### 4. run for inference

所以， 
网络创建时一定要有是否在训练的参数。
