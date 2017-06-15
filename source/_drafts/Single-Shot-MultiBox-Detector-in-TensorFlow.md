---
title: Single Shot MultiBox Detector in TensorFlow
tags:
---


# 目标
- [ ] 看懂网络结构
    - [ ]  vgg
    - [ ]  feature layers
    - [ ]  predictions
        - [ ]  输出 multi box 
- [ ] 看懂代价方程
    - [ ] 
- [ ] 看懂预处理步骤
    - [ ] tf_image_whitened
- [ ] 如何进行的数据的输入
    - [ ] slim的使用
    - [ ] dataset and batch `DatasetDataProvider` for train
    - [ ] single image `tf.placeholder`
- [ ] 如何检测一步一步的结果
    - [ ] tf.InteractiveSession
    - [ ] test 的方式 tf.test.TestCase


# SSD 的原理

使用前向卷积神经网络（feed-forward convolutional network）产生固定长度的一组包围盒（bounding box）,以及这些盒中物体类别（object class）,最后用非极值抑制（non-maximun suppression）产生最后的结果。

## prediction step 网络的创建
Multi-scale feature maps for detection 
基于多尺度特征的检测
在基础网络vgg之上，增加特征层的卷积。这些层逐渐减少尺寸，这样在不同尺度下，可以进行对象的预测。

Convolutional predictors for detection
对每一个增加的特征层可产生一组预测。

主要在`ssd_vgg_300.ssd_net()`内


### 网络结构
`block1 ~ block11` 描述 conv net 的结构

#### vgg
`block1 ~ block5` 
1. conv2d
    - 2个2rep [64, 128]， 
    - 3个3rep [256, 512, 512]
    - block的名字由conv2d组决定
2. max_pool2d
    - 在每个conv2d组后做一个

#### features layers

### 计算multi box的函数
`ssd_vgg_300.ssd_multibox_layer()`

### 预测和定位层
``` python
predictions = []
logits = []
localisations = []
for i, layer in enumerate(feat_layers):
    with tf.variable_scope(layer + '_box'):
        p, l = ssd_multibox_layer(end_points[layer],
                                  num_classes,
                                  anchor_sizes[i],
                                  anchor_ratios[i],
                                  normalizations[i])
    predictions.append(prediction_fn(p))
    logits.append(p)
    localisations.append(l)
```


## train step 代价方程
这个方法被称为[MultiBox method](https://arxiv.org/pdf/1312.2249.pdf)

在训练的时候，我们进行`预测 <-> 真值`的双边匹配（bipartite matching）。例如，如果我们预测了$N$个boxes, 真值有$M$个boxes, 那么就有$N\times M$个匹配的（原子）度量(loss)。

代价loss 分成两个部分
1. 位置代价 localization loss
对象box和预测的最接近的box之间的相似性度量。
2. 可信度代价 confendence loss
候选区域是否目标对象的 logistic loss

### 位置loss of loc
原子度量是
$$dist(l_i, g_j)$$
$l_i$ 是 第 i 个 预测的box的位置， $g_j$ 是 第 j 个真值box的位置。

全部的loss是 $F_{loc}$
$$ F(x,l,g) = \sum_{i, j} dist(l_i, g_j)$$




# 使用 tensorflow 进行目标检测

SSD 参考： https://github.com/balancap/SSD-Tensorflow
https://github.com/oyxhust/ssd-text_detection

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