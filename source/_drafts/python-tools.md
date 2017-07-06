---
title: python tools
tags:
---


# virtualenv

### install on Ubuntu
``` bash
apt install python-pip
pip install virtualenv
```

### 创建默认环境
```
virtualenv --system-site-packages ENV
```
your virtual environment will inherit packages from /usr/lib/python2.7/site-packages


### centos

```
sudo yum install gcc
```

```
sudo yum install gcc-c++
```