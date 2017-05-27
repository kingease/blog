---
title: how to use python generator
tags:
---

假设已经有了一个生层器
```
def gen():
    while True:
        ...
        yeild X
```

1. 生成一个结果
```
next(gen) 
```
2. 一直生成直到结束
```
for i in gen:
    ...
```
3. 生成特定数量