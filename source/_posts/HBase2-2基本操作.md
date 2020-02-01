---
title: HBase2.2基本操作
date: 2019-12-15 13:03:46
category: HBase
tag: HBase
---

## 1. 多版本操作日志

```bash
create 'test', 'cf1' # 创建表test,列族cf1
describe 'test' # 查看表基本信息，默认只存1个版本
alter 'test',{NAME=>'cf1', VERSIONS=>'3'} # 查看表基本信息，改为保存3个版本
put 'test','row1','cf1:name','shenjian' # 插入数据第一个版本
put 'test','row1','cf1:name','domi' # 插入数据第二个版本
scan 'test', {VERSIONS=>3} # 查看多版本数据
```
