---
title: linux deep learning setup
tags:
    - installation
    - GPU
    - ubuntu
    - 驱动
---


# ubuntu 16.04
##  退出图形界面
关闭Xserver
```
sudo init 3
```
切换到文字模式
```
Alt+Ctrl+F1~F6
```

## 启动进入命令行模式
``` 
#GRUB_CMDLINE_LINUX_DEFAULT=”quiet”
GRUB_CMDLINE_LINUX="" 改为 GRUB_CMDLINE_LINUX="text"
GRUB_TERMINAL=console
```

```
sudo update-grub 
sudo systemctl set-default multi-user.target
```

关闭桌面的命令
```
sudo service lightdm stop
```

## GTX1050 安装驱动程序

### 首先要关掉系统默认的nouveau驱动
首先新建文件/etc/modprobe.d/blacklist-nouveau.conf并添加以下内容：
```
blacklist nouveau
options nouveau modeset=0
```
更新一下initramfs，使其生效
```
sudo update-initramfs -u
```

### 删除默认安装的驱动
```
sudo apt-get remove --purge nvidia*
```

### 安装驱动
```
sudo ./NVIDIA-Linux-x86_64-375.26.run --no-x-check --no-nouveau-check --no-opengl-files  
```

## 进入图形界面模式
```
sudo service lightdm start
```

# reference
http://blog.csdn.net/android_chunhui/article/details/78145498?utm_source=debugrun&utm_medium=referral


