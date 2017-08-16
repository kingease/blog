---
title: tensorflow中的各种坑
tags: [tensorflow, 注意]
---

## 1. tf.contrib.layers.fullyconnected。
1. 如果不指定激活函数`activation_fn = None`，激活函数模式是`RELU`而不是线性函数。
2. 激活函数如果想设置成线性函数，**必须**有参数`activation_fn = None`


## 2.1 在tf.reshape的时候如果对None维度报错。
```
logits = tf.reshape(logits, [batch_size, max_time, num_classes])
```

如果
```
shape = rnn_outputs.get_shape()
batch_size, max_time = shape[0], shape[1]
```
其中 `batch_size` 和 `max_time` 通常是 `None`, 会报错，提示需要`int32`。

目标tensor的尺寸由shape()获取，`None`被转换成接收的类型。
```
shape = tf.shape(rnn_outputs)
batch_size, max_time = shape[0], shape[1]
```


## 2.2 在tf.layers.dense时使用tf.shape的输出会报错。
和上一个问题相似，但恰恰需要使用`get_shape()` 来获取非`None`维度，传给`tf.layers.dense`函数，否则也会报错。
一般 shape 的最后一个维度为具体的值而非'None',这正是dense函数所需要的。


## 重复使用`tf.nn.dynamic_rnn`会报错。
报错的内容类似
> Variable rnn/multi_rnn_cell/cell_0/basic_lstm_cell/weights already exists, disallowed.

0. 在调用函数的时候，在某个新创建的图下（只要这个图没有创建过）就不会有already exists的可能。
```
with tf.Graph().as_default():
    ...
```
说明，图在创建和保存图的意义所在。Inputs, ModelLayer, Loss, 真正的实施是在图这个环节。

1. 解决问题的方法一：使用自己的namescope
```
with tf.variable_scope('lstm1'):
    ....
```


## 在tensorflow中避免使用自然的常数要使用`tf.constant`!
否则会出很多奇奇怪怪的问题。

