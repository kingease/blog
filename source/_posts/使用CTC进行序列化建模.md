---
title: 使用CTC进行序列化建模
date: 2018-03-24 17:39:07
tags:
---

## 介绍
考虑语音识别问题。我们有音频剪辑的数据集以及对应的文字脚本。不幸的是，我们不知道文字脚本中的字符和音频如何对应起来。这使得训练一个语音识别器要比我们起初想象的要难。

如果没有对应关系，哪些简单的方法恐怕排不上用场。我们可以设计一个规则，例如“一个字符对应十个(音节)输入”。 但是人们说话的语速变化很大，因此这类规则会经常被打破。另外一种方法是手动的方式标记音频中那一部分对应那个字符。从建模的角度上说，它可以很好的工作，我们知道每一个时间步长上的真值。可是，在不太大的数据集做这种标记恐怕是非常琐碎和耗时的工作，以至于这种方式无法真正的去实践。


这个问题不仅出现在语音识别的问题中。我们在许多地方都有相似的情况。从图片中或者一串笔迹中进行手写识别是一个例子。对视频进行行为标记(Actioin labelling)。
<table style="border:none;"> <tr style="border:none;"> <td style="border:none;"><img src="https://distill.pub/2017/ctc/assets/handwriting_recognition.svg" style="border: none; "></td> <td style="border:none;"><img src="https://distill.pub/2017/ctc/assets/speech_recognition.svg" style="border: none;"></td> </tr> <tr style="border:none;"> <td style="border:none;">手写识别：输入$(x,y)$可以是笔画的坐标或者是图像中的像素值</td><td style="border:none;">语音识别：输入可以是声谱或者是某些其他基于频率的特征</td></tr>

基于时间连接的分类(CTC)是一种在不知道输入输出对齐信息时的解决方法。这里说的对齐就是上述字符对音节的对应关系。 我们会看到，它非常适合用于解决语音识别和手写识别问题。

## 参考
1. https://distill.pub/2017/ctc/