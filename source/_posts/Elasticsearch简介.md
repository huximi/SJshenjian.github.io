---
title: Elasticsearch简介
date: 2019-06-29 08:41:14
category: Elasticsearch
tag: Elasticsearch
---

Elasticsearch(开源分布式搜索引擎): 近实时、分布式存储/搜索/分析引擎

## 1. 起源

Elasticsearch起源于Lucene

+ 基于Java语言开发的搜索引擎类库
+ 高性能、易扩展
+ Lucene的局限性: 
	* 只能基于Java语言开发
	* 类库学习曲线陡峭
	* 原生并不支持水平扩展

## 2. 诞生

+ 2004年Shay Banon基于Lucene开发了Compass
+ 2010年Shay Banon重写Compass, 取名为Elasticsearch
	* 支持分布式，可水平扩展
	* 降低全文检索学习曲线，可以被任何语言调用

## 3. 分布式架构

![Elasticsearch的分布式架构](Elasticsearch简介/Elasticsearch分布式架构.png)

+ 集群规模可以从单个扩展到数百个
+ 高可用&水平扩展
	* 服务和数据两个维度
+ 支持不同的节点类型
	* 支持Hot&Warm架构

## 4. 支持多种继承方式接入

+ 多种编程语言的类库
+ RESTful API v.s Transport API
+ JDBC & ODBC

## 5. 主要功能

+ 海量数据的分布式存储及集群管理
	* 服务与数据两个维度，水平扩展
+ 近实时搜索，性能卓越
	* 结构化/全文/地理位置/自动完成
+ 海量数据的近实时分析
	* 聚合功能

## 6. 版本与新特性

### 6.1 新特性 5.X

+ Lucene 6.X,性能能提升，默认打分机制从TF-IDF改为BM 25
+ 支持Ingest节点/Painless Scripting/Completion suggested支持/原生的Java REST客户端
+ Type标记成deprecated,新增Keyword类型支持
+ 性能优化
	* 内部引擎移除了避免同一文档并发更新的竞争锁，性能提升15%~20%
	* Instant aggregation,支持分片上聚合的缓存
	* 新增了Profile API


### 6.2 新特性 6.X

+ Lucene 7.X
+ 新功能
	* 跨集群复制(CCR)
	* 索引生命周期管理
	* SQL的支持
+ 更友好的升级及数据迁移
	* 在主要版本间升级更为简化
	* 全新的基于操作的数据复制框架，可加快恢复数据
+ 性能优化
	* 有效存储稀疏字段的新方法，降低了存储成本
	* 在索引时进行排序，可加快排序的查询性能

### 6.3 新特性 7.X

+ Lucene 8.0
+ 重大改进-正式废除单个索引下的多Type的支持
+ 7.1开始，Security功能免费使用
+ ECK - Elasticsearch Operator on Kubernetes
+ 新功能
	* New Cluster coordition
	* Future-Complete High Level REST Client
	* Script Score Query
+ 性能优化
	* 默认的Primary Shard数从5改为1，避免Over Sharding
	* 性能优化，更快的Top K