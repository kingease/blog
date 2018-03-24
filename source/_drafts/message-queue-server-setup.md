---
title: message queue server setup
tags:
    - 消息队列
---

# 使用rabbitMQ

## 安装
https://dl.bintray.com/rabbitmq/rabbitmq-server-deb/rabbitmq-server_3.6.12-1_all.deb


## install Erlang/OTP
需要更新版本，安装19.3版本。

```
wget -c http://erlang.org/download/otp_src_19.3.tar.gz
```



## Erlang version pinning
1.
```
cd /etc/apt/perferences.d/
touch erlang 
```

2. copy and paste the following lines into the `erlang` file
```
# /etc/apt/preferences.d/erlang
Package: erlang*
Pin: version 1:19.3-1
Pin-Priority: 1000

Package: esl-erlang
Pin: version 1:19.3.6
Pin-Priority: 1000
"
```
3.


## 使用docker 服务
1. 下载镜像
```
docker pull rabbitmq:3.6.12-management
```

2. 启动container
```
docker run -d --name rabbitmq -p 5671:5671 -p 5672:5672 -p 4369:4369 -p 25672:25672 -p 15672:15672 rabbitmq:3.6.12-management
```
端口的意义
4369 (epmd), 25672 (Erlang distribution)
5672, 5671 (AMQP 0-9-1 without and with TLS)
15672 (if management plugin is enabled)
61613, 61614 (if STOMP is enabled)
1883, 8883 (if MQTT is enabled)

### 搭建集群
1. https://segmentfault.com/a/1190000004394741
2. http://www.cnblogs.com/luo-mao/p/5909889.html

#### 集群 docker github 
3. https://github.com/bijukunjummen/docker-rabbitmq-cluster
4. 
# reference
消息队列介绍、RabbitMQ、Redis
[http://www.cnblogs.com/yunweiqiang/p/7248141.html]
install erlang
[http://blog.csdn.net/zcyzsy/article/details/53008269]

http://blog.csdn.net/column/details/rabbitmq.html