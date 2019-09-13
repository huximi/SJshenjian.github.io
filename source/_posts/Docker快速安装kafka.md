---
title: Docker快速安装kafka
date: 2019-06-23 17:37:22
category: [Docker]
tag: [Docker, Kafka]
---

同样基于docker-compose安装，[Docker快速部署nginx](https://shenjian.online/2019/06/09/Docker%E5%BF%AB%E9%80%9F%E9%83%A8%E7%BD%B2nginx/)中有讲到，不在重述

## 1. 编写docker-compose.yml

个人习惯放在/opt/下，如/opt/kafka, /opt/nginx, docker-compose.yml如下

``` yml
version: '3'
services:
  zookeeper:
    image: wurstmeister/zookeeper
    ports:
      - "2181:2181" # 端口不可省略，否则，docker会内部会随机分配端口，造成zk connected refused
  kafka:
    image: wurstmeister/kafka
    ports:
      - "9092:9092"
    environment:
      KAFKA_ADVERTISED_HOST_NAME: 192.168.153.152 # 宿主IP地址，此为我虚拟机地址
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```

## 2. 启动kafka

`docker-compose up -d`

Creating kafka_kafka_1     ... done
Creating kafka_zookeeper_1 ... done

## 3. 消息生产消费验证

``` bash
docker exec -it kafka_kafka_1 /bin/bash # 进入kafka终端

# 创建test主题，副本1，分区1
kafka-topics.sh --create --topic test --zookeeper kafka_zookeeper_1:2181 --replication-factor 1 --partitions 1

kafka-topics.sh --list --zookeeper kafka_zookeeper_1:2181 # 查看刚才创建的test分区

# 发布消息，输入几条消息后，按^C退出发布
kafka-console-producer.sh --topic=test --broker-list kafka_kafka_1:9092

# 接收消息
kafka-console-consumer.sh --bootstrap-server kafka_kafka_1:9092 --from-beginning --topic test
```

如果正常的话，发布的消息能够接收到，开始愉悦的kafka之旅吧~~~
