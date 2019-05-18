---
title: Lock接口和ReentrantLock
date: 2019-03-29 13:51:42
category: 多线程	
tag: 多线程
---

## 1. Lock接口的特性

Lock接口提供的synchronized所不具备的主要特性：

特性|描述
-|-
尝试非阻塞的获取锁|当线程尝试获取锁，如果该锁没有被其他线程持有，则成功获取并持有锁
能被中断的获取锁|与synchronized不同，获取到锁的线程能够响应中断，当获取到锁的线程被中断时，抛出中断异常，同时锁被释放
超时获取锁|在指定的时间内获取锁，如果超过了指定时间，则返回

## 2. Lock接口的API

方法名称|描述
-|-
void lock()|获取锁，调用该方法当前线程会获取锁，当锁获得后，该方法返回
void lockInterruptibly() throws InterruptedException|可中断的获取锁，和lock()方法不同之处在于该方法会响应中断，即在锁的获取中可以中断当前线程
boolean tryLock()|尝试非阻塞的获取锁，调用该方法后立即返回。如果能够获取到返回true，否则返回false
boolean tryLock(long time, TimeUnit unit) throws InterruptedException|超时获取锁，当前线程在以下三种情况下会被返回:当前线程在超时时间内获取了锁 当前线程在超时时间内被中断 超时时间结束，返回false
void unlock()|释放锁
Condition newCondition()|获取等待通知组件，该组件和当前的锁绑定，当前线程只有获取了锁，才能调用该组件的wait()方法，而调用后，当前线程将释放锁

## 3. Lock接口类图

![Lock接口类图](Lock接口和ReentrantLock/Lock接口类图.png)

## 4. Lock接口使用的模板方法

``` bash
Lock lock = new ReentrantLock();
        ...
        lock.lock();//获取锁
        try {
            ...
        } finally {
            lock.unlock();//释放锁
        }
}
```

不要将锁的获取过程写在try块中。因为如果在获取锁(自定义锁的实现)时，发生了异常，异常抛出的同时，也会导致锁的无故释放。

## 5. ReentrantLock内部组成

ReentrantLock支持公平锁(FairSync)与非公平锁(NonfairSync)。new ReentrantLock()，事实上使用的就是非公平锁

``` bash
public ReentrantLock() {
    sync = new NonfairSync();//非公平锁
}
```

所谓非公平，指的是多个线程同时尝试获取一个锁时，可能会多次被同一个线程获取。实际中公平锁吞吐量比非公平锁小很多，因此我们大多数情况下使用的都是非公平锁。当持有锁的时间较长，或者请求锁的平均时间间隔较长，那么应该使用公平锁。

ReentrantLock内部维护了一个Sync成员对象，其是FairSync和NonfairSync的抽象父类。表面上看锁的功能是由ReentrantLock实现的，实际是由其内部的私有变量Sync来完成的，根据是否需要是公平锁，给Sync提供不同的具体实现。

## 6. synchronized和ReentrantLock之间的选择

ReentrantLock提供了synchronized以外的功能：定时的锁等待、可中断的锁等待、公平性以及非块结构的加锁；
synchronized简单易用，ReentrantLock危险性更高，如果finally块中忘记调用unlock,则埋下定时炸弹；
synchronized还有另一优点，在线程转储中能给出哪些调用帧中获取了哪些锁，并能检测和识别发生死锁的线程，而ReentrantLock在JDK1.6后才支持将其注册进一个管理和调试接口，从而使ReentrantLock相关的加锁信息出现在线程转储中；