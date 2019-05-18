---
title: LockSupport简介及使用
date: 2019-03-25 16:58:44
category: 多线程
tag: 多线程
---

### 1. 简介

LockSupport是JDK中比较底层的类，是用来创建锁和其他同步工具类的线程阻塞基本原语。
JAVA锁和同步工具类的核心AQS: abstractQueuedSynchronizer就是通过调用LockSupport的park()和unpark()实现阻塞和唤醒的。

LockSupport很类似于二元信号量(只有一个许可证可使用)，如果这个许可还没有被占用，当前线程获取许可并继续执行；如果许可已经被占用，当前线程阻塞，等待获取许可

``` bash
LockSupport.park(); // 获取许可
LockSupport.unpark(); // 释放许可
```

特别注意：

**许可默认是占用的** 
**许可是不可重入的**
**许可是可以响应中断的**

### 2. 源码分析

``` bash
public class LockSupport {
	private LockSupport() {} // Cannot be instantiated.

    private static void setBlocker(Thread t, Object arg) {
        UNSAFE.putObject(t, parkBlockerOffset, arg);
    }

     public static void unpark(Thread thread) {
        if (thread != null)
            UNSAFE.unpark(thread);
    }

    public static void park(Object blocker) {
        Thread t = Thread.currentThread();
        setBlocker(t, blocker);
        UNSAFE.park(false, 0L);
        setBlocker(t, null);
    }

    public static Object getBlocker(Thread t) {
        if (t == null)
            throw new NullPointerException();
        return UNSAFE.getObjectVolatile(t, parkBlockerOffset);
    }

    public static void park() {
        UNSAFE.park(false, 0L);
    }

    private static final sun.misc.Unsafe UNSAFE;
    private static final long parkBlockerOffset;

    static {
        try {
            UNSAFE = sun.misc.Unsafe.getUnsafe();
            Class<?> tk = Thread.class;
            parkBlockerOffset = UNSAFE.objectFieldOffset
                (tk.getDeclaredField("parkBlocker"));
        } catch (Exception ex) { throw new Error(ex); }
    }
}
```

##### 2.1 parkBlocker作用

``` bash
public class Thread implements Runnable {
	/**
     * The argument supplied to the current call to
     * java.util.concurrent.locks.LockSupport.park.
     * Set by (private) java.util.concurrent.locks.LockSupport.setBlocker
     * Accessed using java.util.concurrent.locks.LockSupport.getBlocker
     */
    volatile Object parkBlocker;
}
```

parkBlocker是用来记录线程是谁阻塞的，常用于监控与分析线程

``` bash
public static void main(String[] args) throws InterruptedException {
    Thread theadOne = new Thread(() -> {
        LockSupport.park("I'm the blocker");
    });
    theadOne.start();

    Thread.sleep(1000);
    System.out.println(LockSupport.getBlocker(theadOne));
}
```
执行结果：

``` bash
I'm the blocker
```

##### 2.2 parkBlockerOffset作用

`parkBlockerOffset = UNSAFE.objectFieldOffset(tk.getDeclaredField("parkBlocker"));` 偏移量就是该Thread中变量parkBlocker在内存中的偏移量。
对于`getBlocker(Thread t)`方法，对于阻塞的线程，不会响应线程内的方法调用，只有通过内存方式`UNSAFE.getObjectVolatile(t, parkBlockerOffset)`进行获取阻塞对象。