---
title: Thread的join方法
date: 2019-03-18 11:18:22
category: 多线程
tag: 多线程
---

## 1. join的作用

Thread类中有一个join方法，其作用是： **在线程A中调用了线程B的join方法，则线程A需要等待线程B执行完毕后才能继续执行**

## 2. 使用demo

``` bash
public class Temp {

    public static void main(String[] args) throws InterruptedException {
        long start = System.currentTimeMillis();
        System.out.println(Thread.currentThread().getName() + "开始执行");

        Thread threadOne = new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "开始执行");
            try {
                Thread.sleep(10000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "结束执行");
        });

        threadOne.start();

        Thread.sleep(9000);
        long joinStart = System.currentTimeMillis();
        threadOne.join();
        System.out.println(Thread.currentThread().getName() + "执行完毕，共花费" + (System.currentTimeMillis() - start) / 1000 + "S," +
                "其中等待threadOne时间为" + (System.currentTimeMillis() - joinStart) / 1000 + "S");
    }
}
```

执行结果：

``` bash
main开始执行
Thread-0开始执行
Thread-0结束执行
main执行完毕，共花费10S,其中等待threadOne时间为1S
```

## 3. 使用场景

分库查询。如果某个页面的数据需要来自两个数据库，在A中查询需要9秒，在B中查询需要10秒，如果采用join方式，两个线程进行执行，先执行完的等待后执行完的，则总共执行花费10秒
