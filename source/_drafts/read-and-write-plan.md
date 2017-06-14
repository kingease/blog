---
title: read and write plan
tags:
---

## 阅读
#### 基础的理解
1. [神经网络可表示任何函数的直观证明](http://neuralnetworksanddeeplearning.com/chap4.html)

#### word2vec
##### 预处理
1. （字典）词汇<-->数字 的映射表：vocab_to_int, int_to_vocab
    2. int_words是文本经过vocab_to_int映射后的产物 
2. （词频表）词频的统计表：Counter(int_words) 
3. 减少噪音词汇（词频很低的词汇，比如少于5次的词汇）
3. 从int_words中随机删除部分词频过高的词（而不是字典）

##### 分批化
定义好输入(x)和输出(y)的维度 1 -> n
1. x的表示 1 个数值
2. y的表示 n 个数值
3. 生成一个batch的(x, y)

#### seq2seq
1. https://github.com/ematvey/tensorflow-seq2seq-tutorials
2. [tensorflow实现RNN](http://www.wildml.com/2016/08/rnns-in-tensorflow-a-practical-guide-and-undocumented-features/)
3. [RNN教程 part1 介绍](http://www.wildml.com/2015/09/recurrent-neural-networks-tutorial-part-1-introduction-to-rnns/)

### 其他
[理解用CNN进行NPL](http://www.wildml.com/2015/11/understanding-convolutional-neural-networks-for-nlp/)

[用tesorflow实现CNN文本分类](http://www.wildml.com/2015/12/implementing-a-cnn-for-text-classification-in-tensorflow/)

[svm和softmax的比较](http://cs231n.github.io/linear-classify/#svmvssoftmax)

[Q learning](https://medium.com/emergent-future/simple-reinforcement-learning-with-tensorflow-part-0-q-learning-with-tables-and-neural-networks-d195264329d0)

## 写作

[svm的理解]

[KKT条件的理解]

[floyd 的使用](https://www.floydhub.com/)

如何进行预处理和分批化？

有约束条件的神经网络如何构建？

思维的几个层级, 接口也有这样的逻辑？
1. (single): 1 个 x -> 1 个 y
2. (batch): n 个 x -> n 个 y
    3. 考虑接口，是不是 element-wise 的
    4.  如：
3. 将 n 个 合成一个 
    1. cost ： 使用 tf.reduce_mean 


### 神经网络层的种类
#### 嵌入层
    1. 输入: tf.placeholder(tf.int32, [<>])

### 教程
Deep Learning for Computer Vision 
http://imatge-upc.github.io/telecombcn-2016-dlcv/


### python 语言
[with statement]
http://effbot.org/zone/python-with-statement.htm

