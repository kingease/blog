---
title: 在AWS上如何使用GPU
date: 2017-04-30 14:24:25
tags: [tensorflow, GPU, AWS]
---

## 在AWS上使用GPU?
1. 确保删除了cpu版的tensorflow， 否则，安装完gpu版后再删除，就会出现不可用的情况
   ```bash
   sudo `which pip` uninstall tensorflow
   ```
2. 安装GPU版的tensorflow
   ```bash
   sudo `which pip` install -U tensorflow-gpu
   ```
3. 查看GPU的使用情况
   ```
   nvidia-smi
   ```


## 如何检测机器是否有GPU支持?
```python
import tensorflow as tf
gpu_dev_name = tf.test.gpu_device_name()
if not gpu_dev_name:
    print("Default GPU Device:{}".format(gpu_dev_name))
```
