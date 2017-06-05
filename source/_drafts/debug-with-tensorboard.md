---
title: debug with tensorboard
tags:
---

## 将网络规则化
首先要对各个层，赋予有意义的`name_scope`，如
```
with tf.name_scope("inputs_images"):
    ....
```
这样tensorboard可以生成清晰的模型的数据流图，如下图
![rnn+ctc model graph](http://oor53bfqy.bkt.clouddn.com/graph-run-version-01.png)


## 在我们关注的地方加入输出


整个模型跑起来的，但是效果非常的糟糕,cost的值据高不下

## 观察输出定位问题

发现RNN的输出始终都是相同的，如下图，对任何输入都是如下图的模样：
![rnn output image](http://oor53bfqy.bkt.clouddn.com/RNN%20output%203000.png?imageMogr2/thumbnail/350x)

## 调整
1. RNN层增加`dropout`发现效果没有改变。 
2. RNN层的初始化有问题，输入对RNN层而言，似乎没有差别，不然输出怎么没有区别？

沿着这个角度想，使用了batch_normalization的处理，可没有效果。后来发现
```
tf.layers.batch_normalization(gray_images)
```
应该改成
```
tf.layers.batch_normalization(gray_images, training=True)
```
模型终于在迭代的过程中出现变化。
有一篇文章在说这个问题。https://r2rt.com/implementing-batch-normalization-in-tensorflow.html

training == True: normalized with statistics of the current batch
training == False: normalized with `moving statistics`

三个不同的输入
<figure style="display: flex;">
    <img style="margin: 20px;" src="http://oor53bfqy.bkt.clouddn.com/individualImage4.png">
    <img style="margin: 20px;" src="http://oor53bfqy.bkt.clouddn.com/individualImage5.png">
    <img style="margin: 20px;" src="http://oor53bfqy.bkt.clouddn.com/individualImage6.png">
</figure>
RNN层的输出变成这个样子：
![rnn output image 1](http://oor53bfqy.bkt.clouddn.com/individualImage1.png?imageMogr2/thumbnail/350x)
![rnn output image 2](http://oor53bfqy.bkt.clouddn.com/individualImage2.png?imageMogr2/thumbnail/350x)
![rnn output image 3](http://oor53bfqy.bkt.clouddn.com/individualImage3.png?imageMogr2/thumbnail/350x)

## 变化的结果

在迭代数个周期后，终于有了效果：cost开始减少, 呈现收敛的状态。
![cost decrease](http://oor53bfqy.bkt.clouddn.com/cost-curve.png)

error 也逐渐变得很小，也就是正确率可以变得很高。
![error decrease](http://oor53bfqy.bkt.clouddn.com/error-curve.png)

## 仍然存在的问题

1. 感觉有很强烈的台阶感，中间迭代的部分下降很缓慢。
2. 初始化的过程，影响结果。
3. 对batch_normalization还不是太了解。
4. 如何区分 training 为 True 或 False 的使用。
5. 保存模型。
6. 使用CNN对输入图像做特征的处理。

