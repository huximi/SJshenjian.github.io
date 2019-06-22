---
title: happens-before规则
date: 2019-06-16 10:55:54
category: 多线程
tag: 多线程
---

happends-before是JMM最核心的概念，理解happends-befores是理解JMM的关键。

## 1. happends-before关系定义如下

1) 如果一个操作happends-before另一个操作，那么另一个操作执行的结果将对第二个操作可见，而且第一个操作的执行顺序排在第二个操作之前
2) 两个操作之间存在happends-before关系，并不意味着java平台的具体实现必须要按happends-before关系执行的顺序来执行，如果重排序之后的执行结果，与按happends-before关系来执行的结果一致，那么这种重排序并不非法。

## 2. happends-before规则

1) 程序顺序规则： *一个线程的每个操作，happends-before于该线程中任意的后续操作*
2) 监视器锁规则： *对一个锁的解锁，happends-before于任意后续对这个锁的加锁*
3) volatile变量规则： *对一个volatile的写，happends-before于任意后续对该volatile域的读*
4) 传递性： 如果A happends-before B 且 B happends-before，那么 A happends before C 
5) start()规则: 如果线程A执行操作ThreadB.start(),那么A线程的ThreadB.start()操作happends before于线程B的任意操作
6) join()规则：如果线程A执行操作ThreadB.join()方法并成功返回，则线程B中的任意操作happends before于线程A从ThreadB.join()操作成功返回