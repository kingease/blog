---
title: attention mechnisim
tags:
---

http://www.wildml.com/2016/01/attention-and-memory-in-deep-learning-and-nlp/

# 注意与记忆

But only recently have attention mechanisms made their way into recurrent neural networks architectures that are typically used in NLP (and increasingly also in vision). That’s what we’ll focus on in this post.

但直到最近 注意机制 才被引入迭代神经网络架构(RNN), NLP和视觉领域。


> 注意力机制解决什么问题？

以神经网络翻译机器为例，

传统的翻译系统依赖于复杂的特征工程
这些特征基于文本的统计特性。

而 NMT 将桔子用一个固定长度的向量表示。没有以来 类似 n-gram 统计 而是文本更高阶的意义。

更重要的是，NMT 更容易构建和训练，不需要人工的特征工程。



