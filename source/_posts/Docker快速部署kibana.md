---
title: Docker快速部署kibana
date: 2019-07-07 10:56:04
category: Docker
tag: [Docker,Kibana]
---

基于CentOS7，VMWare虚拟机给其分配了2G内存, **防火墙已关闭**

## 1. 编写docker-compose.yml

``` yml
version: '2'
services:
  kibana:
    image: docker.elastic.co/kibana/kibana:7.2.0
    ports:
      - "5601:5601" # 再次强调，一定要做端口映射,否则无法远程访问
    #volumes: 先注释挂载，后面后讲到原因
    #  - ./kibana.yml:/usr/share/kibana/config/kibana.yml
```

## 2. 启动kibana并复制kibana.yml文件

`docker-compose up -d`启动kibana后，`docker ps -a`查看启动后的容器ID为0481b574ce11

执行复制命令
将容器中的kibana.yml文件复制到本机/opt/kibana/kibana.yml
**这样避免因为不同版本的差异导致配置文件不同，从网络上搜索的配置文件与版本不兼容问题**

`docker cp 04:/usr/share/kibana/config/kibana.yml /opt/kibana/kibana.yml`

## 3.修改docekr-compose.yml文件与kibana.yml文件(kibana版本为7.2.0)

``` yml
version: '2'
services:
  kibana:
    image: docker.elastic.co/kibana/kibana:7.2.0
    ports:
      - "5601:5601"
    volumes: # 放开注释，配置文件挂载
      - ./kibana.yml:/usr/share/kibana/config/kibana.yml
```

``` yml
server.name: kibana
server.host: "0.0.0.0"
elasticsearch.hosts: [ "http://192.168.153.151:9200" ] # kibana依赖于elasticsearch,请先确保其正常启动，修改IP地址为elasticsearch地址
elasticsearch.requestTimeout: 90000 # 默认30000
xpack.monitoring.ui.container.elasticsearch.enabled: true
```

### 4. 重新启动，远程访问

`docker-compose down`
`docker-compose up -d`

http://192.168.153.151:5601开始kibana之旅吧