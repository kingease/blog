---
title: read code of tensorflow attention ocr
tags:
---


https://github.com/tensorflow/models/tree/master/attention_ocr


# 目前的目标
- [ ] 读懂代码
- [ ] 先不训练，而是先做模型加载和预测


# 代码
使用的方法是 seq 到 seq 
有 encoder 和 decoder 两个层

> 用到类似方法的文章 What You Get Is What You See: A Visual Markup Decompiler

用到 spatial attention 的信息， 但是没有用到文字的包围盒信息

> 有一个问题，如果输出的字符串的长度有多长？他决定decoder unrolling 的的长度。
所以是不是 ssd 才是一个选择，找到可能的再识别？

用到了自回归 autoregression 


## train 调用 common_flags.create_model

## common_flags.create_model 产生 Model对象

Model对象的init只做了参数的初始化。

## Model 分解成 几个环节

### 创建基础模型 create_base()
这个部分创建的估计只是 inference 部分必备的 组成。

### 创建目标函数 create_loss()
这个部分是为训练创建的评价函数 ：代价函数

### 训练


## Model 的 create_base() 中的子过程
### 首先 conv_tower_fn 提取图像特征
用 `inception_v3` 计算图像的feature
### pool_views_fn
将多个view的features组合成一个feature

