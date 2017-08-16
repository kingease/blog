---
title: encode decode in python
tags:
---


> 什么是encode?

encode 是将特定的编码的数据如utf-8, 表示成字符流


> 什么是decode?

decode 是将字符流转成特定编码的数据，如utf-8



> python2.7 我们如何读一个utf-8的数据文本文件呢？

当我们
```
with open(filename) as f:
    for line in f:
        ....
```
数据是字符流，我们需要将其转成utf-8的数据。这时候，我们要使用decode函数。

这和我们的直觉相反，记住`转成字符流用encode， 转成特定的用decode. 文件保存的是字符流数据`。

