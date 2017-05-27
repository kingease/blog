---
title: Tensorflow的FAQ
date: 2017-04-03 10:44:18
tags: [tensorflow, setup]
---

## LSTM 中有一个state是干什么的？
- `state`是隐变量`h(t)`它是模型的输入也是模型的输出。计算中有两个公式要保存。
    + 初始的`init_state`。初始化由`cells`层的`zero_state(batch_size, tf.float32)`给出。模型中要保存这个量。`zero_state`这个接口的参数应该再查一下，为什么是`batch_size`?
    + 计算的`final_state`。的是由`tf.nn.dynamic_rnn`给出。

## 如何load一个保存的模型?
这个设计也真是奇葩，为了恢复模型`feed_dict`中的变量->创建图->创建session->创建loader->加载源文件->为session恢复模型->通过图和名称恢复变量。真是可怕！
1. 创建tensorflow的`Graph`
```python
loaded_graph = tf.Graph()
```
2. 创建`session`时指定创建的用来加载模型的图`loaded_graph`
```python
with tf.Session(graph=loaded_graph) as session:
    pass
```
3. 创建模型的加载器并读取`meta`文件
``` python
with tf.Session(graph=loaded_graph) as session:
    loader = tf.train.import_meta_graph('model-file.meta')
```
4. 加载器载入模型文件
```python
with tf.Session(graph=loaded_graph) as session:
    loader = tf.train.import_meta_graph('model-file.meta')
    loader.restore(session, 'model-file')
```
5. 用`loaded_graph`恢复变量
```python
x = loaded_graph.get_tensor_by_name('x:0')
```




