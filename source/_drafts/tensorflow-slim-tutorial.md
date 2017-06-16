---
title: tensorflow slim tutorial
tags:
---

## slim 的工程的结构
1. datasets
2. preprocessing
3. nets
4. deployment
5. checkpoints

### 1. datasets
训练时，读取使用的数据集，分片等，所有的数据集都要封装成module。

### 2. preprocessing
图像的预处理函数 不同的网络，可能有不同的预处理函数，如：
1. vgg
2. lenet
3. ssd_vgg

### 3. nets
1. 存放网络结构,网络结构中不包含`INPUTS`层的定义，但默认好作为输入。
2. nets有istraining开关，决定是否使用在训练还是推断阶段。

### 4. deployment
暂时不知道，可能跟部署有关

### 5. checkpoints
存放训练的保存的模型

## Train的组件与流程

## Evaluation的组件与流程

## Inference的组件与流程

## 参考
[slim doc](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/slim)
[官方样板工程](https://github.com/tensorflow/models/tree/master/slim)
[mnist工程](https://github.com/mnuke/tf-slim-mnist)
[ssd tf工程](https://github.com/balancap/SSD-Tensorflow)
[slim notebook](https://github.com/tensorflow/models/blob/master/slim/slim_walkthrough.ipynb)