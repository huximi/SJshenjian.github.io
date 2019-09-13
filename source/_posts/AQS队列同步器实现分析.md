---
title: AQS队列同步器实现分析
date: 2019-06-22 09:43:55
category: 多线程
tag: 多线程
---

## 1. 同步队列

AQS内部依靠同步队列(FIFO双向队列)来管理同步状态，当线程获取同步状态失败时，AQS通过构造Node节点将线程加入同步队列，同步状态释放时，会把首节点的线程唤醒，使其再次尝试获取同步状态。

### 1.1 Node节点

``` java
static final class Node {
	    /** waitStatus value to indicate thread has cancelled */
        static final int CANCELLED =  1;
        /** waitStatus value to indicate successor's thread needs unparking */
        static final int SIGNAL    = -1; // 后继节点的线程处于等待状态
        /** waitStatus value to indicate thread is waiting on condition */
        static final int CONDITION = -2; // 表明节点在等待队列中 当其他线程调用了Condition的singnal()方法后，该节点会转移至同步队列
        /**
         * waitStatus value to indicate the next acquireShared should
         * unconditionally propagate
         */
        static final int PROPAGATE = -3;

		volatile int waitStatus; // 等待状态(CANCELLED、SIGNAL、CONDITION、PROPAGATE) 
		volatile Node prev;  // 前驱节点
		volatile Node next; // 后继节点
		Node nextWaiter; // 等待队列中的后继节点
		volatile Thread thread; // 获取同步状态的线程
}
```

### 1.2 节点加入同步队列

线程获取同步状态失败后，构造为Node节点加入同步队列，主要通过CAS compareAndSetTail(Node except, Node update)设置尾节点。

### 1.3 节点移出同步队列 

同步队列首节点的线程在释放同步状态时(waitStatus=SINGAL)，将会唤醒后继节点，由于首节点为获取同步状态成功的线程，故无需CAS设置后续节点为首节点即可。

## 2. 独占式同步状态获取与释放



## 3. 共享式同步状态获取与释放

## 4. 独占式超时获取同步状态
