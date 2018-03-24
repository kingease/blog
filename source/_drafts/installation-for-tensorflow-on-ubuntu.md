---
title: installation for tensorflow on ubuntu
tags:
    - installation
    - ubuntu
    - GPU
    - tensorflow
---

## 安装 cuda
```
sudo apt install nvidia-cuda-toolkit
```

https://developer.nvidia.com/cuda-downloads
```
sudo dpkg -i cuda-repo-ubuntu1604_8.0.61-1_amd64.deb
sudo apt-get update
sudo apt-get install cuda
```

### 强制指定特定版本的cuda安装
如果cuda的版本已经被nvidia 更新了，想指定特定的版本
```
sudo apt-get install cuda=8.0.61-1
```

install public CUDA GPG key
```
sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub
```
''
https://developer.nvidia.com/cudnn
安装 cudnn, 需要先注册，下载安装


https://pan.baidu.com/share/link?uk=25789816&shareid=983088990

cd进入解压之后的include目录
```
sudo cp cudnn.h /usr/local/cuda/include/ #复制头文件
```
再cd进入lib64目录下
```
sudo cp lib* /usr/local/cuda/lib64/ #复制动态链接库
```

cd /usr/local/cuda/lib64/


#reference 
http://crescentmoon.info/2017/02/23/install-tensorflow-with-gpu-support-for-ubuntu/

