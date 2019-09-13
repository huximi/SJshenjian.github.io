---
title: Docker快速部署elasticsearch
date: 2019-06-30 21:03:14
category: Docker
tag: [Docker,Elasticsearch]
---

基于CentOS7，VMWare虚拟机给其分配了2G内存, **防火墙已关闭**

## 1. 编写docker-compose.yml

``` yml
version: '2.2'
services:
  es01:
    image: elasticsearch:7.2.0
    container_name: es01
    environment:
      - node.name=es01
      - discovery.seed_hosts=es02
      - cluster.initial_master_nodes=es01,es02
      - cluster.name=docker-cluster
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms256m -Xmx256m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - esdata01:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - esnet
  es02:
    image: elasticsearch:7.2.0
    container_name: es02
    environment:
      - node.name=es02
      - discovery.seed_hosts=es01
      - cluster.initial_master_nodes=es01,es02
      - cluster.name=docker-cluster
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms256m -Xmx256m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - esdata02:/usr/share/elasticsearch/data
    networks:
      - esnet

volumes:
  esdata01:
    driver: local
  esdata02:
    driver: local

networks:
  esnet:
```

PS: [Elasticsearch官方docker-compose.yml](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html)

## 2. 设置vm.max_map_count

``` bash
# vm.max_map_count： 一个进程可以拥有的VMA(虚拟内存区域)的数量
# 单个JVM能开启的最大线程数为vm.max_map_count的一半，即10W线程
# 临时性修改 本质262144写入文件/proc/sys/vm/max_map_count中
sysctl -w vm.max_map_count=262144

# [建议]永久性无法修改/proc/sys/vm/max_map_count文件，但linux中允许直接修改/etc/sysctl.conf,故直接在该文件末添加参数并查看
sysctl -p # 使得修改立即生效，即使重启也生效
grep vm.max_map_count /etc/sysctl.conf

```

[官网](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html)说明：
The vm.max_map_count kernel setting needs to be set to at least 262144 for production use. 

## 3. 启动并查看相关信息

执行`docker-compose up -d`启动

[root@centos7 elasticsearch]# docker container ls
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                              NAMES
ccd713fa8ff6        elasticsearch:7.2.0   "/usr/local/bin/dock…"   55 seconds ago      Up 51 seconds       0.0.0.0:9200->9200/tcp, 9300/tcp   es01
7d1ff4a506d0        elasticsearch:7.2.0   "/usr/local/bin/dock…"   55 seconds ago      Up 51 seconds       9200/tcp, 9300/tcp                 es02

http://192.168.153.151:9200/
{
  "name" : "es01",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "1FJ55641T7ignLt7APzQVQ",
  "version" : {
    "number" : "7.2.0",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "508c38a",
    "build_date" : "2019-06-20T15:54:18.811730Z",
    "build_snapshot" : false,
    "lucene_version" : "8.0.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}

http://192.168.153.151:9200/_cat/health
1561904921 14:28:41 docker-cluster green 2 2 0 0 0 0 0 0 - 100.0%

http://192.168.153.151:9200/_cat/nodes
172.18.0.3 55 95 7 0.23 2.63 3.50 mdi * es01
172.18.0.2 51 95 7 0.23 2.63 3.50 mdi - es02

单机多实例启动成功~~~~

`docker container stop ccd713fa8ff6`停掉es01时，访问：

http://192.168.153.151:9200 显示结果不变

http://192.168.153.151:9200/_cat/health
1561905162 14:32:42 docker-cluster green 1 1 0 0 0 0 0 0 - 100.0%

http://192.168.153.151:9200/_cat/nodes
172.18.0.3 40 68 0 0.02 0.69 2.26 mdi * es01