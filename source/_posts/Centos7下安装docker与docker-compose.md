---
title: Centos7下安装docker与docker-compose
date: 2019-04-21 11:46:59
category: Docker
tag: Docker
---

## 1. Docker yum方式安装

``` bash
# 查看系统版本 
## Docker 运行在 CentOS 7 上，要求系统为64位、系统内核版本为 3.10 以上。 建议采用7，可能6.5会有依赖问题
## Docker 运行在 CentOS-6.5 或更高的版本的 CentOS 上，要求系统为64位、系统内核版本为 2.6.32-431 或者更高版本。
uname -r 

# 移除旧版本
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine

# 安装必要系统工具
yum install -y yum-utils device-mapper-persistent-data lvm2

# 添加软件源信息
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 更新yum缓存
yum makecache fast

# 安装docker-ce
yum install -y docker-ce

# 启动docker后台服务
systemctl start docker
```

## 2. docker-compose安装

``` bash

# 安装pip
yum install -y epel-release
yum install -y python-pip

pip --version # 确认版本

pip install --upgrade pip # 更新pip

pip install docker-compose # 安装docker-compose

docker-compose version # 查看版本
```

## 3. 配置镜像加速器

[容器镜像服务控制台](https://cr.console.aliyun.com/?spm=a2c4g.11186623.2.13.1c2a11beryqiKB)

``` bash
# XXXXX 阿里云为你分配的加速地址
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["<your accelerate address>"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

最后`chkconfig docker on`开机自启动，免得每次虚拟机关闭后手动启动 