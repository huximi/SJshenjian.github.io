---
title: AbstractQueuedSynchronizer简介与使用
date: 2019-03-27 09:10:02
category: 多线程
tag: [多线程,设计模式]
---

AbstractQueuedSynchronizer是并发包中最核心的类，没有之一，它是在LockSupport与Unsafe的基础上实现的。
AQS使用的主要方式是继承，我们编写一个类继承AbstractQueuedSynchronizer，重写特定的方法，来实现不同功能的同步组件。

### 1. 同步组件简介

同步组件从总体可分为两种：

**独占式同步组件(又称独占锁)：** 在任何时刻只有一个线程获得锁，可以执行，其他线程阻塞，进入等待队列。

**共享式同步组件(又称共享锁)：** 共享锁允许多个线程同时执行，通常情况下，共享锁内部维护了多个执行许可，每个线程运行的时候获得一个许可，结束的时候释放一个许可，如果共享锁已经没有了许可，则线程进入等待队列

注意：可以认为独占锁是共享锁的一个特例，只有一个许可。
从这个角度理解的话，独占锁与共享锁都应该提供**获取许可**与**释放许可**的功能。

**获取许可：** 如果线程获取许可成功，则有执行的权力，许可数量-1，获取失败，则阻塞，进入等待队列
**释放许可：** 拥有执行许可的线程运行结束后，则释放许可，许可数量+1，同时负责唤醒等待队列中的线程

可以看到，在获取许可和释放许可的过程中，有两个重要的内容需要维护：可用许可的数量、等待队列

### 2. AQS支持同步组件原理

队列同步器AbstractQueuedSynchronizer作为构建同步组件的基础框架。支持独占式的获取同步状态，也支持共享式获取同步状态。ReentryLock、ReentryReadWriteLock、CountDownLatch等都是在AQS的基础上实现的。

AQS对可用许可数量与等待队列均提供了支持：

**可用许可数量的支持：** 它使用了int类型state变量表示执行许可，初始状态为0，我们限定许可数量为5个，线程获取到一个许可，state+1, 表明已经使用了几个许可
**等待队列的支持：** 通过内置的FIFO队列来完成线程的排队工作，队列的节点通过静态内部类Node实现

需要注意的是，AQS对等待队列是完全支持的，也就是对开发者完全屏蔽了阻塞线程入队与出对的操作细节；；而对可用数量(state)提供了部分支持，需要开发者重写特定的方法才能正常工作

AQS中定义了8个模板方法，对应4个需要开发者覆写的方法：

组件类型|模板方法|需要覆写的方法
-|-|-|
独占式同步组件|void acquire(int arg)|boolean tryAcquire(int arg)
|void acquireInterruptibly(int arg)|
|boolean tryAcquireNanos(int arg, long nanosTimeout)|
|boolean release(int arg)|boolean tryRelease(int arg)
共享式同步组件|void acquireShared(int arg)|boolean tryAcquireShared(int arg)
|void acquireSharedInterruptibly(int arg)|
|boolean tryAcquireSharedNanos(int arg, long nanosTimeout)|
|boolean releaseShared(int arg)|boolean tryReleaseShared(int arg)

### 3. 编写同步组件需要注意的地方

1)使用新的接口和实现包装同步组件：在我们编写一个同步组件的时候，例如我们想实现一个独占锁，假设为Sync，其继承了AQS。只需要在Sync类中覆写tryRelease和tryAcquire即可，但是由于继承AQS的时候，会把tryAcquireShared、tryReleaseShared等共享锁方法也继承下来。而Sync并不会实现这些共享式同步组件的方法，因为Sync只是一个独占锁而已，从业务含义上，因此应该将这些方法屏蔽，从而防止用户误操作。按照最佳实现，屏蔽的方式是定义一个新的接口，假设用Mutex表示，这个接口只定义了独占锁相关方法，再编写一个类MutexImpl实现Mutex接口，而对于同步组件Sync类的操作，都封装在MutexImpl中。

2)同步组件推荐定义为静态内部类：因为某个同步组件通常是为实现特定的目的而实现，可能只适用于特定的场合。如果某个同步组件不具备通用性，我们应该将其定义为一个私有的静态内部类。结合第一点，我们编写的同步组件Sync应该是MutexImpl的一个私有的静态内部类。

### 4. 基于AQS实现独占锁示例

``` bash
public interface Mutex {

    void acquire();

    void release();
}
```

``` bash
public class MutexImpl implements Mutex {

    private Sync sync = new Sync();

    @Override
    public void acquire() {
        sync.acquire(1);
    }

    @Override
    public void release() {
        sync.release(1);
    }

    private static class Sync extends AbstractQueuedSynchronizer {

        @Override
        protected boolean tryAcquire(int arg) {
            return super.compareAndSetState(0, 1);
        }

        @Override
        protected boolean tryRelease(int arg) {
            return super.compareAndSetState(1, 0);
        }


    }
}
```

``` bash
public class Main {

    public static void main(String[] args) {
        Mutex mutex = new MutexImpl();
        for (int i = 0; i < 10; i++) {
            new Thread(new MutexThread(mutex)).start();
        }
    }
}

class MutexThread implements Runnable {

    private Mutex mutex;

    public MutexThread(Mutex mutex) {
        this.mutex = mutex;
    }

    @Override
    public void run() {
        String name = Thread.currentThread().getName();
        mutex.acquire();
        System.out.println(name + "获得锁开始执行");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            mutex.release();
            System.out.println(name + "释放锁结束运行");
        }
    }
}
```

执行结果：

``` bash
Thread-0获得锁开始执行
Thread-1获得锁开始执行
Thread-0释放锁结束运行
Thread-2获得锁开始执行
Thread-1释放锁结束运行
Thread-2释放锁结束运行
Thread-4获得锁开始执行
Thread-4释放锁结束运行
Thread-3获得锁开始执行
Thread-3释放锁结束运行
Thread-5获得锁开始执行
Thread-5释放锁结束运行
Thread-6获得锁开始执行
Thread-6释放锁结束运行
Thread-7获得锁开始执行
Thread-7释放锁结束运行
Thread-8获得锁开始执行
Thread-8释放锁结束运行
Thread-9获得锁开始执行
Thread-9释放锁结束运行
```