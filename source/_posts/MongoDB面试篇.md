---
title: MongoDB面试篇
date: 2019-10-14 21:09:11
category: MongoDB
tag: [MongoDB,面试]
---

## MongoDB面试篇

### 1. 什么是MongoDB

MongoDB是一个文档数据库，提供好的性能，领先的非关系型数据库，采用BSON存储文档数据[引申问题BSON与JSON的区别]

### 2. MongoDB是由哪种语言写的

MongoDB是用C++写的，MySQL也是用C++写的

### 3. MongoDB的优势有哪些

面向文档的存储：文档存储以BSON格式(有大小限制，最大16M), 内置GridFS文件系统(一般存储大于16M的文件)。
+ 任何属性都可以建立索引
+ 复制及高扩展性
+ 自动分片
+ 丰富的查询功能
+ 快速的及时更新
+ 来自MongoDB的专业支持

### 4. 什么是集合

集合是一组MongoDB文档，类似于关系型数据库中表的概念。一个集合中的多个文档可以有多个不同的字段。一般来说，集合中的文档有着相同或相关的目的

### 5. 什么是文档

文档是由一组key-value组成。文档是动态模式，意味着集合中的每个文档不需要有相同的字段或结构。非关系型数据库表中的一条记录相当于一个文档

### 6. 什么是mongod,常用参数有什么

mongod是处理MongoDB的主要进程。它处理数据请求，管理数据存储，执行后台管理操作。当我们运行mongod命令意味着MongoDB正在启动并且在后台运行。

`mongod --dbpath /data/db --port 27017 #默认数据库路径与端口`

### 7. 什么是mongo

mongo是一个命令行工具用于连接特定的mongod实例。当我们没有指定参数时，则使用默认的端口与localhost进行连接

### 8. MongoDB常用命令

```bash
# MongoDB用use+数据库名称的方式来切换数据库，无该数据库，则自动创建后切换
user database_name

show dbs # 查看数据库列表

db.dropDatabase() # 删除当前数据库

db.createCollection(name, options) # 创建集合

show collections # 查看创建的集合

db.collection.drop() # 删除集合

db.collection.createIndex() # 创建索引

db.collection.find() # 查询集合中的索引

db.collection.find().pretty() # 查询集合中的索引并格式化输出

# 使用"AND"或"OR"条件循环查询集合中的文档
db.collection.find(
	{
		$or: {
			{key1: value1}, {key2: value2}
		}
	}
)

# 更新数据
db.collection.update(
	{key: value},
	{$set: {newKey: newValue}}
)

db.collection.remove({key: value}) # 删除文档

db.collection.find({key: value}).sort({columnName: -1}) # 在MongoDB中排序 1|升序 -1|降序

db.collection.aggregate(aggregate_operation) # 聚合
```

### 9. 什么是非关系型数据库

非关系型数据库是相对于传统关系型数据库的统称，显著特点是不使用SQL作为查询语言，数据存储不需要特定的表格存储。由于其简单的设计与非常好的性能被广泛应用于大数据与Web Apps等

### 10. 非关系型数据库有哪些类型

+ Key-Value存储 eg: Redis
+ 图表 eg: Neo4J
+ 文档存储： MongoDB ElasticSearch
+ 基于列储存： Cassandra

### 11. 为什么用MongoDB

+ 架构简单
+ 没有复杂的连接查询
+ 深度查询能力，支持动态查询
+ 容易调试、扩展
+ 不需要转化/映射应用对象到数据库对象
+ 使用内部内存作为存储工作区，更快的存取数据

### 12. 在哪些场景使用MongoDB

+ 大数据
+ 内存管理系统
+ 移动Apps
+ 数据管理

### 13. MongoDB的命名空间是什么意思

MongoDB存储BSON对象在丛集(collection)中,数据库名字与丛集以点号连接起来叫命名空间(namespace)
一个集合命名空间包含多个数据域(extend),集合命名空间存储着集合的元数据，如集合名称、第一个数据域的位置与最后一个数据域的位置。
而一个数据域由若干条文档(document)组成，每个数据域都有一个头部，记录这第一个文档和最后一个文档的位置，以及该数据库的一些元数据。
extend之间、document之间以双向链表连接
索引的数据结构是B树，索引命名空间存储B树根节点的指针

### 14. MongoDB中的分片是什么意思

分片将数据水平切分到不同的物理节点。当应用数据越来越大时，数据量也会越来越大。当数据量增长时，单台机器有可能无法存储数据或者可接受的读写吞吐量。利用分片技术，可以添加更多的机器来应对数据量增加与读写操作的要求

### 15. 如何查看使用MongoDB中的连接

`db.adminCommand({"connPoolStats": 1})`

### 16. 什么是复制

MongoDB复制是将数据同步到多个服务器的过程
+ 保障数据的安全性
+ 数据高可用
+ 数据灾难恢复
+ 无需停机维护(如备份、重建索引、压缩)
+ 分布式读取数据

### 17. 为什么要在MongoDB中使用分析器

分析器用于显示每个操作性能特点，通过分析器可以找到比预期慢的查询或写操作，利用这一信息，比如可以确定是否需要添加索引

### 18. MongoDB支持主键外键关系吗

默认MongoDB不支持主键外键关系。用MongoDB本身的API硬编码才能实现外键关联，不够直观且难度较大

### 19. MongoDB支持哪些数据类型

+ String 
+ Integer 
+ Double 
+ Boolean
+ Object
+ Object ID 
+ Arrays 
+ Min/Max Keys
+ Datetime
+ Code
+ Regular Expression等

### 20. 为什么在MongoDB中使用Code数据类型

"Code"类型用于在文档中存储Javascript代码

### 21. 为什么要在MongoDB中用"Regular Expression"数据类型

"Regular Expression"类型用于在文档中存储正则表达式

### 22. 为什么在MongoDB中使用"Object ID"数据类型

"Object ID"类型用于存储文档id

### 23. "Object ID"有哪些部分组成

一共有四部分组成：时间戳、客户端ID、客户进程ID、三个字节的增量计数器

### 24. MongoDB中什么是索引

索引用于高效的执行查询，没有索引的MongoDB将扫描集合的所有文档，这种扫描效率很低，需要处理大量的数据。
索引的数据结构是B树，能够存储某种特殊字段或字段集的值，并按照索引指定的方式将字段值排序

### 25. 在MongoDB中什么是副本集

副本集由一组MongoDB实例组成，包括一个主节点多个从节点，客户端所有数据都写入主节点，从节点从主节点同步写入数据，避免单点故障，数据高可用
