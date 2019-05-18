---
title: docker运行常用软件命令
date: 2019-04-21 11:47:59
category: docker
tag: docker
---

``` bash
# 新建一个name为redis的容器并运行redis
## -p 主机端口：容器端口 --name 容器名 -v 挂载目录，规则与端口映射相同 -d 后台启动 redis-server 已配置文件启动redis --appendonly yes 开启redis持久化
docker run --name redis -p 6379:6379  -v /usr/local/docker/redis.conf:/etc/redis/redis.conf -v /usr/local/docker/data:/data -d redis:4.0 redis-server /etc/redis/redis.conf --requirepass password --appendonly yes

# 新建一个name为mysql的容器运行mysql
## -e MYSQL\_ROOT\_PASSWORD=123456 设置登录密码为123456
docker run --name mysql -p 6379:6379 mysql:5.7 -e MYSQL\_ROOT\_PASSWORD=123456 -d 

# springboot jar包运行
nohup java -jar XXX.jar --server.port=8081 &
```