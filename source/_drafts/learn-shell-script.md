---
title: learn shell script
tags:
---


## 指定脚本的执行程序
```
#!/bin/bash
```


## 将脚本设置成为可执行文件
```bash
chmod +x test.sh
```

## 定义变量
```
our_name="qinjx"
```

## 使用变量
```
echo ${your_name}
echo $your_name
```

可以认为脚本只有字符串和数字两种类型

## 字符串
1. 单引号字符串
2. 双引号字符串（可以出现转义字符和变量）
3. 