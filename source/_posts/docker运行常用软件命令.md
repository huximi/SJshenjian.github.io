---
title: Docker运行常用软件命令
date: 2019-04-21 11:47:59
category: Docker
tag: Docker
---

``` bash
# 启动(系统为centos7）
systemctl start docker

systemctl status docker 

# 新建一个name为redis的容器并运行redis
## -p 主机端口：容器端口 --name 容器名 -v 挂载目录，规则与端口映射相同 -d 后台启动 redis-server 已配置文件启动redis --appendonly yes 开启redis持久化
docker run --name redis -p 6379:6379  -v /usr/local/docker/redis.conf:/etc/redis/redis.conf -v /usr/local/docker/data:/data -d redis:4.0 redis-server /etc/redis/redis.conf --requirepass password --appendonly yes

# 新建一个name为mysql的容器运行mysql
## -e MYSQL\_ROOT\_PASSWORD=123456 设置登录密码为123456
docker run --name mysql -p 6379:6379 mysql:5.7 -e MYSQL\_ROOT\_PASSWORD=123456 -d 

# 列出所有容器
docekr ps -a

# 列出已经上线的容器
docekr container ls

# 列出镜像
docker image ls

# 将镜像push至远程仓库并在其他机器pull，如docker.io
docker login ## 首先登陆

docker tag mongo:4.0 haotu369/mongo:4.0 ## 将本地镜像重命令为带有docker.io账号的形式，如我的账号为haotu369

docker push haotu369/mongo:4.0 ## push至远程仓库

# 从远程仓库下载镜像
docker pull haotu369/mongo:4.0 ## push至本地

# springboot jar包运行
nohup java -jar XXX.jar --server.port=8081 &
```