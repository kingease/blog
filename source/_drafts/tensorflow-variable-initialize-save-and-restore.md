---
title: tensorflow variable initialize save and restore
tags:
---


在训练模型的时候， 我们使用变量保存和更新模型的参数。
我们必须显示的初始化、保持这些变量。
方便将来恢复这些模型。
主要有两个类：

1. tf.Variable
2. tf.train.Saver



