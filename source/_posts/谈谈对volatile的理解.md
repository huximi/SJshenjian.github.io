---
title: 谈谈对volatile的理解
date: 2019-01-10 14:35:06
category: 多线程
tag: [面试,多线程]
---

synchronized关键字、Lock对象同时支持原子性、有序性和可见性；volatile变量支持有序性、可见性，不支持原子性。

## 保证可见性

可见性是对于多CPU而言，单CPU不存在可见性问题
1）立即将工作内存中寄存器里存储的修改后的变量写回到主内存中，主要通过使load/store指令一并执行实现(注意内存模型中规定read/load、 store/write必须成对出现)
2）其他处理器通过嗅探总线上传播过来的数据监测自己工作内存中缓存是否过期。过期，则从主存中重新读取。

double与long为非原子的64位操作，用volatile修饰，可保证其安全性

## 保证有序性

通过内存屏障禁止指令重排序。具体是通过在volatile修饰的变量赋值后，多执行一个lock addI $0X0, (%esp)操作。  --ps ESP为32位堆栈顶指针


## 实际使用场景--状态变量标记

一般业务场景中用于 volatile static Boolean flag = true



