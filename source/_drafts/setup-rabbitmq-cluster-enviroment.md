---
title: setup cluster enviroment
tags:
---

# prerequirment

## install curl
```
sudo apt install curl
```

## install docker
ubuntu
```
sudo apt-get install docker
sudo apt-get install docker.io
```

### 显示 docker images
```
sudo docker ps
```

### docker 加速
```
$ curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://9884941f.m.daocloud.io
$ sudo systemctl restart docker.service
```

### 拉取镜像 这个镜像可建立多节点
```
sudo docker pull bijukunjummen/rabbitmq-server:3.6.10
```
or build from source code
```
git clone https://github.com/bijukunjummen/docker-rabbitmq-cluster.git
```

### 配置hosts
#### 在物理机ubuntu-recogition1上
```
# 设置其他节点ip
sudo echo "xxx.xxx.xxx.xxx  ubuntu-primary-services" >> /ect/hosts
```
#### 在物理机ubuntu-primary-services上
```
# 设置其他节点ip
sudo echo "xxx.xxx.xxx.xxx  ubuntu-recogition1" >> /ect/hosts
```


### start rabbitmq 服务
#### 在物理机ubuntu-recogition1上
```
sudo docker run -d --hostname ubuntu-recogition1 --name rabbit1 \
-p 4369:4369 \
-p 5671:5671 \
-p 5672:5672 \
-p 15671:15671 \
-p 15672:15672 \
-p 25672:25672 \
-v /etc/hosts:/etc/hosts \
bijukunjummen/rabbitmq-server:3.6.10
```

#### 在物理机ubuntu-primary-services上
```
sudo docker run -d --hostname ubuntu-primary-services --name rabbit1 \
-p 4369:4369 \
-p 5671:5671 \
-p 5672:5672 \
-p 15671:15671 \
-p 15672:15672 \
-p 25672:25672 \
-v /etc/hosts:/etc/hosts \
-e CLUSTERED=true \
-e CLUSTER_WITH=ubuntu-recogition1 \
bijukunjummen/rabbitmq-server:3.6.10
```

端口的意义
4369 (epmd), 25672 (Erlang distribution)
5672, 5671 (AMQP 0-9-1 without and with TLS)
15672 (if management plugin is enabled)
61613, 61614 (if STOMP is enabled)
1883, 8883 (if MQTT is enabled)

#reference
1. http://www.cnblogs.com/cuishuai/p/7193335.html
2. http://blog.csdn.net/xuyaqun/article/details/50957254
3.http://www.cnblogs.com/sfissw/p/5497874.html
