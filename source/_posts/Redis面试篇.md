---
title: Redis面试篇
date: 2019-10-08 20:36:49
category: Redis
tag: [Redis,面试]
---

## Redis常见问题总结

### 1. 使用redis有哪些好处？

+ 速度快 基于内存，查找操作时间复杂度O(1)
+ 支持多种数据类型存储 string/list/set/sorted set/hash
+ 支持事务 操作都是原子性，要么全部执行，要么全部不执行
+ 可用于缓存、消息、按key设置过期时间，过期自动删除 

### 2. Redis与memcached有哪些优势？

+ memcached值仅为string,redis支持多种数据类型
+ redis速度比memcached快很多
+ redis可持久化其数据

### 3. Redis常见性能问题与解决方案？

+ Master最好不要做任何持久化工作，如RDB内存快照和AOF日志记录
 [可选回答]Master写内存快照，save命令调度rdbSave函数,会阻塞主线程工作，当快照比较大时，会间断性暂停服务; Master AOF持久化，如果不重写AOF文件，这个持久化方式对性能的影响是最小的，但是AOF文件会不断增大，AOF文件过大会影响Master重启的恢复速度，Master调用BGREWRITEAOF重写AOF文件，会占大量的CPU与内存资源，导致服务load过高，出现短暂服务暂停现象
+ 如果数据比较重要，某个Slave设置AOF备份数据,策略设置每秒同步一次
+ 为了主从复制的速度与连接的稳定性，Master与Slave最好在同一个局域网内
+ 尽量避免在压力很大的主库上增加从库
+ 主从复制不要用图状结构，用单向链表结构更为稳定。如Master <- Slave1 <- Slave2

### 4. MySQL里有2000w数据，redis中只存20w的数据，如何保证redis中的数据都是热点数据？

redis内存数据集上升到一定大小的时候，就会执行数据淘汰策略。redis有6种数据淘汰策略
+ volatile-lru 从已设置过期时间的数据集挑选最近最少使用的数据淘汰
+ volatile-ttl 从已设置过期时间的数据集挑选即将过期的数据淘汰
+ volatile-random 从已设置过期的数据集任意选择数据淘汰
+ allkeys-lru 从数据集挑选最近最少使用的数据淘汰
+ allkeys-random 从数据集任意选择数据淘汰
+ no-enviction 禁止驱逐数据

### 5. Zookeeper四种类型的znode？

+ PERSISTENT:客户端与zookeeper断开连接后，节点不会删除
+ PERSISTENT_SEQUENTIAL: 客户端与zookeeper断开连接后，节点不会删除且进行顺序编号
+ EPHEMERAL: 客户端与zookeeper断开连接后，节点删除
+ EPHEMERAL_SEQUENTIAL: 客户端与zookeeper断开连接后，节点删除且顺序编号

### 6. Redis与Memcache的区别都有哪些？

+ 存储方式 memcache数据全部存在于内存中，断电丢失;redis支持数据持久化
+ 数据支持类型 memcache数据类型简单；redis支持五种数据类型
+ 使用底层模型不同 redis直接使用自己的构建的VM机制，因为一般的系统调用系统函数的话，会浪费一定的时间去移动与请求
+ value大小
redis最大可达到1GB,而memcache只有1MB

### 7. Redis最适合的场景？

redis最适合所有数据in-memory的场景，虽然也提供了持久化功能，但更多的是disk-back的功能
实际项目使用场景： 存储JWT,用户授权验证；定时模块分布式执行控制(在多台设备中仅执行一次)

### 8. Redis的同步机制了解吗？

主从同步。第一次同步时，主节点做一次bgsave,并同时将后续修改操作记录至内存buffer,待完成后将rdb文件全量同步到复制节点，复制节点接收完成后将rdb镜像加载到内存，加载完成后，在通知主节点将期间修改的操作记录过来进行重放就完成了同步过程

### 9. 是否使用过Redis集群，集群的原理是什么？

Redis Sentinel着眼于高可用，在master宕机时会自动将slave提升为master，继续提供服务。
Redis Cluster着眼于扩展性，在单个redis内存不足时，使用Cluster进行分片存储。

参考来源：
[interview_internal_reference/10.Redis篇](
https://github.com/0voice/interview_internal_reference/blob/master/10.Redis%E7%AF%87)