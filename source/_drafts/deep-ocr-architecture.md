---
title: deep ocr architecture
tags:
---





##  词的侦测 Word Detector

### 基于深度网络的方法
主要的方法有 RCNN , 目前用于物体包围盒（bounding boxes）的定位, 如猫，狗，一般是 1~5个给定的物体。

### 传统的计算机视觉方法
MSER(最大稳定极值区域)，该方法在不同的阈值下(thresholds or levels), 来寻找斑块(blobs), 对文本而言，是一个很好的方法。


## 词的深度网 Word Deep Net

### 深度网
1. CNN
2. LSTM
3. CTC
4. batch normalization

1. 设置输入图像的大小 height:128, width:512
2. 验证模型的可工作性


input images:
    1. 长和宽的长度是固定的（以后更长的需要，主动切断图像，目前不在考虑范围之内）
    2. 目前只支持灰白图（减少输入的变化范围）


### 生成训练数据
1. 生成足够真实的图像是很困难的。目前一个可能的方法是GANS
2. 在我们的问题里，使用合成的图像是适用的。
3. 文字和它的变化是有限的。

合成数据的流程：
1. 所用词的语料 这里主要是数字，小数点，符号
2. 所使用的字体
3. 一组几何(gemoetric)和光度(photometric)的变化来模拟真实场景带来的变化
4. a large number of visual transformations, such as warping, fake shadows, and fake creases, and much more.

