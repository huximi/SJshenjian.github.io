---
title: HBase2.2初装与Java客户端连接
date: 2019-12-14 18:45:52
category: HBase
tag: HBase
---

## 1. HBase目录结构

![结构](1.PNG)

## 2. 启动

`./bin/start-base/sh`

![启动](start.PNG)


## 3. 查看是否启动成功

访问 http://192.168.153.171:16010/

![ui](ui.png)

**注意：serverName为centos7,常见的Java连接HBase UnknowHostException一般由此产生**

执行`hostname`查看虚拟机名称，发现centos7名称正好与serverName吻合

![hostname](hostname.png)

## 4. 虚拟机hostname修改

临时修改：`hostname 192.168.153.171` 单机非集群版改为IP,省去DNS解析，简单粗暴
永久修改： 
1）`vim /etc/hostname`将centos7改为192.168.153.171
2）执行`systemctl restart network.service`重启网络

![ip](ip.png)
![update](update.png)

## 5. 重启Hbase

执行`./bin/stop-base/sh`，如果一直...,则执行`jps`查看HMaster进程，杀死即可

![restart](restart.png)
访问 http://192.168.153.171:16010/, 发现serverName已经更改过来
![updated](updated.png)

## 6. 与shell交互

执行`./bin/hbase shell`进入交互页面
执行以下命令创建表并查看表
![crud](crud.png)

## 7. JAVA客户端连接

### 7.1 创建maven项目并引入pom.xml

```xml
    <dependencies>
        <!-- https://mvnrepository.com/artifact/org.apache.hbase/hbase-client -->
        <dependency>
            <groupId>org.apache.hbase</groupId>
            <artifactId>hbase-client</artifactId>
            <version>2.2.2</version>
        </dependency>
    </dependencies>
```

### 7.2 resource资源目录下新建log4j.properties文件

```properties
log4j.rootLogger=INFO, stdout

log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout

# Pattern to output the caller's file name and line number.
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] (%F:%L) - %m%n

log4j.logger.org.apache=WARN
```

### 7.3 JAVA代码编写

```java
public class PutExample {

    public static void main(String[] args) throws IOException {
        Configuration conf = HBaseConfiguration.create();
        // qurom与clientPort默认属性位于hbase-common包中hbase-default.xml中，见下图
        conf.set("hbase.zookeeper.quorum", "192.168.153.171");
        conf.set("hbase.zookeeper.property.clientPort", "2181");
        // 设置重试次数,第一次连接为了及时打印结果，重试1次
        conf.set("hbase.client.retries.number", "1");
        Admin admin = ConnectionFactory.createConnection(conf).getAdmin();
        if (admin != null) {
           TableName[] tableNames = admin.listTableNames();
           for (TableName name : tableNames) {
               System.out.println(name);
           }
        }
    }
}
```

![qurom](qurom.png)

执行结果：
![result](result.png)

