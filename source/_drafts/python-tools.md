---
title: python tools and enviroments
tags:
---


# virtualenv

### install on Ubuntu
``` bash
apt install python-pip
pip install virtualenv
```

# install nvidia 的组件
https://www.tensorflow.org/install/install_sources


### 创建默认环境
```
virtualenv --system-site-packages ENV
```
your virtual environment will inherit packages from /usr/lib/python2.7/site-packages

### jupyter executeable
有的时候，我们创建虚拟环境
但在jupyter 里查看
```
import sys
sys.executable
```
发现并不是当前的虚拟环境，这是因为kernel被指向了其他的位置。

显示当前jupyer kernel 的列表位置
```
jupyter kernelspec list
```
显示
```
Available kernels:
  python2           /Users/yangli/Library/Jupyter/kernels/python2
  python2tickets    /Users/yangli/Library/Jupyter/kernels/python2tickets
  vpython           /Users/yangli/Library/Jupyter/kernels/vpython
  python3           //anaconda/envs/gait/share/jupyter/kernels/python3
```
如果没有就在这个对应的目录下创建kernel. 并在jupyter 中切换

### centos

```
sudo yum install gcc
```

```
sudo yum install gcc-c++
```

## 如果遇到源有问题的时候, 如何临时修改源

```
pip install pika -i https://pypi.python.org/simple
```