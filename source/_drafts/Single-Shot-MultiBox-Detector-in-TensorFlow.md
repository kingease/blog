---
title: Single Shot MultiBox Detector in TensorFlow
tags:
---


# 使用 tensorflow 进行目标检测

SSD 参考： https://github.com/balancap/SSD-Tensorflow

SSD 是 目标检测 的统一架构。

由三个部分组成：
1. datasets
2. pre-processing:预处理和数据增强，（VGG和Inception的启发）
3. networks

## 2. preprocessing
在 preprocessing 的模块内

## 3. networks
在 nets 模块内
SSD 网络的定义和编码（encoding）和解码(decoding)的过程。

检测过程由两个步骤组成：
1. 在图像上作用SSD模型。
    - 构建模型 ssd_vgg_300
2. 在输出上执行后处理算法。
    - top-k filter
    - Non-maximun Suppression

这里需要使用 transfer learning 中的一些方法。 比如把 VGG的网络迁移到我们的计算过程。

VGG迁移在笔记 `transfer-deep-networks.md` 中。

主要使用的是tf.contrib.slim 中的功能
https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/slim

