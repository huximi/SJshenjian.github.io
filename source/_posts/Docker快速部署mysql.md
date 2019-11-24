---
title: Docker快速部署mysql
date: 2019-11-23 10:56:04
category: Docker
tag: [Docker,Mysql]
---

基于CentOS7，VMWare虚拟机给其分配了2G内存, **防火墙已关闭**

## 1. 编写docker-compose.yml

``` yml
version: '3'
services:
  db:
    image: mysql
    container_name: mysql57
    restart: always
    environment:
      MYSQL_USER: shenjian
      MYSQL_PASSWORD: 123456
      MYSQL_DATABASE: database
      MYSQL_ROOT_PASSWORD: 123456
    command:
      --default-authentication-plugin=mysql_native_password
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_general_ci
      --explicit_defaults_for_timestamp=true
      --lower_case_table_names=1
      --max_allowed_packet=128M
    ports:
      - 3306:3306
    volumes:
      - ./data:/var/lib/mysql
```

## 2. 启动mysql

`docker-compose up -d`启动mysql后，`docker ps -a`查看启动后的容器ID为7c6c1e35b9d2

OK, 有了docker，一切都是这么简单