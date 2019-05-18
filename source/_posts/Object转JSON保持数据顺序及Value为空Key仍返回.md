---
title: Object转JSON日期格式化并保持数据顺序及Value为空Key仍返回
date: 2019-01-10 17:13:35
category: 工作干货
tag: 工作干货
---

## 业务背景

（1）项目全景不可配置化，过程修改返回字段前端页面也需相应修改，可扩展性差。
（2）要想可配置化，字段需从过程中取，但AJAX返回的JSON数据KEY顺序不与过程中返回字段顺序一致

## 原因分析

fastjson在Object转JSON的时候，底层采用HashMap, 若有序，则需为LinkedHashMap, 查看源码发现

```bash
 public JSONObject(int initialCapacity, boolean ordered){
        if (ordered) {
            map = new LinkedHashMap<String, Object>(initialCapacity);
        } else {
            map = new HashMap<String, Object>(initialCapacity);
        }
    }
```

可是Object如何转为JSON字符串，查看源码并没有发现转的方法，但是过程中返回的是标准的Map类型，故贴上以下代码

## 解决代码

```bash
SerializeConfig serializeConfig = new SerializeConfig();
ObjectSerializer serializer = new SimpleDateFormatSerializer(DateUtils.format());
serializeConfig.put(Timestamp.class, serializer);
serializeConfig.put(java.sql.Date.class, serializer);
serializeConfig.put(Date.class, serializer);

if (obj instanceof Map){
    JSONObject jsonObject = new JSONObject(16, true);
    jsonObject.putAll((Map) obj);
    // 输出key时是否使用双引号,默认为true
    // 是否输出值为null的字段,默认为false
    result = JSON.toJSONStringZ(jsonObject, serializeConfig, SerializerFeature.QuoteFieldNames, SerializerFeature.WriteMapNullValue);
}
```
