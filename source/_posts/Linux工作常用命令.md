---
title: Linux工作常用命令
date: 2019-01-17 17:20:35
category: Linux
tag: Linux
---

``` bash
# 定位到第一行(连续按两个g)
gg
# 定位到最后一行
G

# 查看软件安装目录
which ftp

# 查看端口占用情况
netstat -anp | grep 端口号
netstat -tunlp

# 创建FTP用户默认登录目录
useradd -d /opt/tomcat shenjian

# 密码设置
passwd shenjian

# 为FTP用户授权目录操作权限
chown -hR shenjian /opt/tomcat/

# 目录权限设置
chmod 755 /opt/tomcat

# 解压
## -C 指定目标目录
tar -zxvf [源文件] -C [目标目录]

# 压缩
tar -zcvf [目标压缩文件] [源文件]

# 本地安装
yum localinstall *.rpm

yum install net-tools # netstat安装

# rpm安装
## ivh 安装显示安装进度--install--verbose--hash
rpm -ivh XXX.rpm

# 执行post请求
curl -v -X POST http://localhost:8772/actuator/bus-refresh

uname -r # 查看系统信息

########## 性能监控调优 ##########
# 线程上下文切换次数查看 
## 1|每秒 10|统计10次, 结果中cs是context switch,即上下文切换次数
vmstat 1 10 

# 进程PID查看
jps

# 转储pid=1159的线程信息至文件dump1
jstack 1159 > /opt/temp/dump1

# 线程运行状态打印统计
## sort|排序 uniq 去重与空行 -c 统计打印次数 
grep java.lang.Thread.State /opt/temp/dump1 | awk '{print $2$3$4$5}' | sort | uniq -c

# Linux线程模型查看
## linuxthreads-0.10(该线程模型一个进程限制创建1024个线程) 或者 NPTL 2.12(无限制)
getconf GNU_LIBPTHREAD_VERSION

# 操作系统线程栈大小查看 
## 操作系统线程栈默认10M,JVM栈内存默认1M
## 一个Java进程中可以创建的线程数主要是受到JVM可以使用的内存大小的影响，而不是操作系统的总内存。
ulimit -s

# 虚拟机Centos7网络配置及DNS
## ifcfg-ens后可能不一样 /etc/sysconfig/network-scripts/ifcfg-ens32  /etc/sysconfig/network-scripts/ifcfg-lo
sudo ls /etc/sysconfig/network-scripts/ifcfg-*
vim /etc/sysconfig/network-scripts/ifcfg-ens32

# centos7 防火墙
firewall-cmd --state # 查看防火墙状态
systemctl stop firewalld.service # 关闭防火墙
systemctl disable firewalld.service # 禁止开机自启动
```

以下为windows常用命令

``` bash
# 查看端口占用情况
netstat -ano|findstr 端口号

# 强制查杀指定进程 
##-t 终止指定的进程和由它启用的子进程 
##-f 指定强制终止进程
taskkill /pid 进程号 -t -f

# 查找指定PID并杀死进程
## /f "tokens=2"： 文件的第二个属性 %%a： 变量(特别注意，在单独执行命令时使用%a，在脚本中执行时使用%%a) /fi： 过滤 /nh： 不显示列标题
for /f "tokens=2" %%a in ('tasklist /fi "imagename eq soffice.exe" /nh') do taskkill /pid %%a  -t -f
```