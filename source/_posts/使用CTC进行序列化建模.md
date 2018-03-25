---
title: 使用CTC进行序列化建模
date: 2018-03-24 17:39:07
tags:
---

## 介绍
考虑语音识别问题。我们有音频剪辑的数据集以及对应的文字脚本。不幸的是，我们不知道文字脚本中的字符和音频如何对应起来。这使得训练一个语音识别器要比我们起初想象的要难。

如果没有对应关系，哪些简单的方法恐怕排不上用场。我们可以设计一个规则，例如“一个字符对应十个(音频)输入”。 但是人们说话的语速变化很大，因此这类规则会经常被打破。另外一种方法是手动的方式标记音频中那一部分对应那个字符。从建模的角度上说，它可以很好的工作，我们知道每一个时间步长上的真值。可是，在不太大的数据集做这种标记恐怕是非常琐碎和耗时的工作，以至于这种方式无法真正的去实践。


This problem doesn’t just turn up in speech recognition. We see it in many other places. Handwriting recognition from images or sequences of pen strokes is one example. Action labelling in videos is another.
插图
Connectionist Temporal Classiﬁcation (CTC) is a way to get around not knowing the alignment between the input and the output. As we’ll see, it’s especially well suited to applications like speech and handwriting recognition.

## 参考
1. https://distill.pub/2017/ctc/