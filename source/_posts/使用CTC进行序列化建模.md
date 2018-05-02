---
title: 使用CTC进行序列建模
date: 2018-03-24 17:39:07
tags:
categories:
    - Machine Learning
---

## 介绍

考虑语音识别问题。我们有音频剪辑的数据集以及对应的文字脚本。不幸的是，我们不知道文字脚本中的字符和音频如何对应起来。这使得训练一个语音识别器要比我们起初想象的要难。

如果没有对应关系，哪些简单的方法恐怕排不上用场。我们可以设计一个规则，例如“一个字符对应十个(音频信号)输入”。 但是人们说话的语速变化很大，因此这类规则会经常被打破。另外一种方法是手动的方式标记音频中那一部分对应那个字符。从建模的角度上说，它可以很好的工作，我们知道每一个时间步长上的真值。可是，在不太大的数据集做这种标记恐怕是非常琐碎和耗时的工作，以至于这种方式无法真正的去实践。


这个问题不仅出现在语音识别的问题中。我们在许多地方都有相似的情况。从图片中或者一串笔迹中进行手写识别是一个例子。对视频进行行为标记(Actioin labelling)。
<table style="border:none;"> <tr style="border:none;"> <td style="border:none;"><img src="https://distill.pub/2017/ctc/assets/handwriting_recognition.svg" style="border: none; "></td> <td style="border:none;"><img src="https://distill.pub/2017/ctc/assets/speech_recognition.svg" style="border: none;"></td> </tr> <tr style="border:none;"> <td style="border:none;">手写识别：输入$(x,y)$可以是笔画的坐标或者是图像中的像素值</td><td style="border:none;">语音识别：输入可以是声谱或者是某些其他基于频率的特征</td></tr>

基于时间连接的分类(CTC)是一种在不知道输入输出对齐信息时的解决方法。这里说的对齐就是上述字符对音节的对应关系。 我们会看到，它非常适合用于解决语音识别和手写识别问题。

-----

接下来，我们用更正式的方式描述这个问题, 我们考虑映射，将输入序列$X = [x_1, x_2, \ldots, x_T]$，比如音频信号， 变成对应的输出序列$Y = [y_1, y_2, \ldots, y_U]$，比如文本信息。 我们想寻找一个准确的函数将$\lbrace X\rbrace$映射成$\lbrace Y \rbrace$。

使用简单的监督学习算法这里会遇到些挑战，比如：
* $X$ 和 $Y$ 在长度上是变化的，不是固定的长度。
* $X$ 和 $Y$ 的长度比也是不固定的。
* 我们在$X$和$Y$的元素之间没有准确的对应关系（没有对齐)。

CTC 算法可以解决上述问题。例如给定$X$，它可以给出$\lbrace Y \rbrace$中所有$Y$的概率。我们可用这个分布推断一个可能的输出或者给出指定某个结果的概率。

并不是所有计算损失函数(loss function)以及进行预测(inference)都是简单易行的。我们希望CTC可以在两方面都能有高效的表现。

**损失函数**: 对给定的输入，我们希望训练一个模型使得正确的结果拥有最大的概率。为了实现目标，我们需要高效的计算条件概率$p(Y|X)$。并且函数$p(Y|X)$也应当可微，方便我们使用梯度下降进行求解。

**推断**: 在我们训练得到模型后，我们用它根据输入$X$推断一个可能的的$Y$。这意味需要求解:
$$ Y^* = \arg\max_Y p(Y|X)$$

理想情况下，有CTC的帮助，我们通过求解一个不太消耗的问题，就可以得到期望的$Y^*$。

-----

## 算法

对给定的$X$，CTC算法可以给出任意$Y$的概率值。CTC计算概率的关键是考虑输入和输出间如何进行对齐。我们先探讨如何进行对齐，然后再说明如何使用对齐的概念来计算损失函数以及进行推断的。


### 对齐

CTC算法是不需要输入和输出数据间的对齐信息。但是，要在给定的输入下给出一个输出的概率， CTC需要累加所有输入输出可能的对齐操作的概率。我们需要知道对齐的概念最终帮助我们理解损失函数是如何计算的。

在引入具体的形式的CTC算法前，我们先考虑一种原始的方法。我们用一个例子来说明,假设输入的数据的长度为6而输出是$Y=[c,a,t]$，有3个字符。对齐$X$和$Y$的方式是对每一步输入都赋予一个输出的字符，输出的结果是合并重复的字符。
<img src="https://distill.pub/2017/ctc/assets/naive_alignment.svg" style="border: none; display: block;margin-left: auto;margin-right: auto;">

这个方法存在两个问题：

第一，它必须要每一个输入步都要对起到某些输出上，这不太合理。在语音识别方面，例如，输入可能会有没有对应输出的无声状态。

第二，如果，在有一行中有多个字符的情况下，我们可能无法获得输出。考虑对齐$[h, h, e, l, l, l, o]$,合并重复的结果是"$helo$"而不是"$hello$"

为了解决这个问题，CTC为输出引入了一个新的标识符。这个标识符有时被称作空白标识(blank token)。我们这里把它记做$\epsilon$。这个$\epsilon$标识符不对应任何输出，而只是简单地从输出中剔除。

CTC允许的对齐选项和输出有相同的长度。所有的对齐选项，只要在合并重复以及剔除$\epsilon$后，与$Y$相同就是合法的：

<img src="https://distill.pub/2017/ctc/assets/ctc_alignment_steps.svg" style="border: none; display: block;margin-left: auto;margin-right: auto;">

1. 首先，合并重复的字符
2. 然后，剔除所有$\epsilon$空白符
3. 最后，剩下的字符输出

如果$Y$在一行中有两个相同的字符，那么有效的对齐在它俩中间一定会有一个$\epsilon$。在这个规则的帮助下，我们可以区分"hello"和"helo"两者间不同的对齐选项的区别。

让我们看一个例子，长度为6的输入，输出为$[c,a,t]$。下面有有效和无效的对齐选项的样例：

<img src="https://distill.pub/2017/ctc/assets/valid_invalid_alignments.svg" style="border: none; display: block;margin-left: auto;margin-right: auto;">

无效对齐的情形：
1. 对应的$Y=[c,c,a,t]$
2. 长度为5
3. 缺少'a'

CTC对齐有些非常好的特性。首先，$X$和$Y$间允许的对齐是单调的。当从一个输入变到另外一个输入，输出可能会保持不变。第二个属性是反之亦然。它意味着第三个属性：$Y$的长度不会超过$X$长度。

### 损失函数（loss function）

CTC对齐方法给我们一个自然的方式可以从每一步的概率得到整个输出序列的概率。

<img src="https://distill.pub/2017/ctc/assets/full_collapse_from_audio.svg" style="border: none; display: block;margin-left: auto;margin-right: auto;">

1. 我们从一个输入序列开始，比如音频的声谱
2. 输入经过一个RNN处理
3. 神经网络给出$p_t(a|X)$, 它是对输入的每一步的输出在$\lbrace h, e, l, o \epsilon, \rbrace$上的分布。
4. 有了每一步的概率，我们可以计算不同序列的概率。
5. 剔除重复和空白字符，得到输出的分布。


用CTC训练的模型通常用于循环神经网络(RNN)来估计每一步的概率$p_t(a_t|X)$。一个RNN因为它考虑到输入的上下文，通常表现的很有效。但是我们还是可以选择使用任意学习算法来给出输出的分类。

如果我们不小心仔细，CTC 损失值的计算的花销可能非常的大。


## 参考
1. https://distill.pub/2017/ctc/