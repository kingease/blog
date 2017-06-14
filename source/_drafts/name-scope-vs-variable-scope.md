---
title: name_scope vs variable_scope
tags:
---


tf 在创建 tensor 和 Layer 的时候，会为 tensor 创建名字。 
其实返回的变量只是 创建的 tensor 和Layer的 handle。
而创建tensor和Layer才是我们真正关心的东西。
这些核心东西的名称是叠加了variable_scope的。

1. name_scope 用于 tensor_board
2. variable_scope 用于 weights 的 reuse 在不同的name_scope下

tf.get_variable()
tf.variable_scope()