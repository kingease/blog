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
   ```bash
   nvidia-smi
   ```
4. 报错处理
   ``` bash
   echo "export LD_LIBRARY_PATH=/usr/local/cuda/lib64/" >> ~/.bashrc
   ```

## 如何检测机器是否有GPU支持?
```python
import tensorflow as tf
gpu_dev_name = tf.test.gpu_device_name()
if not gpu_dev_name:
    print("Default GPU Device:{}".format(gpu_dev_name))
```


## 在AWS上挂载卷
1. 创建挂载的路径
   ```bash
   sudo mkdir mount_point
   ```
2. 查看可用的卷
   ``` bash
   lsblk
   ```
   显示的内容里关注
   ```
   NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   xvda    202:0    0   90G  0 disk
   └─xvda1 202:1    0   90G  0 part /
   xvdf    202:80   0  140G  0 disk
   └─xvdf1 202:81   0   90G  0 part /mnt/data
   xvdg    202:96   0  120G  0 disk
   ```
3. 格式化刚增加的卷
```
sudo file -s /dev/xvdg
sudo mkfs -t ext4 /dev/xvdg
```
3. 挂载卷
   ```bash
   sudo mount /dev/xvdg /data
   ```


