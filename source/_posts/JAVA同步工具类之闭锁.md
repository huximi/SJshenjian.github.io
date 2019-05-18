---
title: JAVA同步工具类之闭锁
date: 2019-02-24 16:49:24
category: 多线程
tag: 多线程
---

闭锁是一种同步工具类，可以延迟线程的进度直到其到达终止状态。
闭锁相当于一扇门，在闭锁结束之前，这扇门一直关闭，没有任何线程能够通过；当闭锁结束时，这扇门打开，所有的线程均可通过

闭锁状态包括一个计数器，该计数器初始化为一个正数，表示需要等待的事件数量。countDown方法递减计数器，表示一个事件已经发生，如果计数器非零，则await方法等待计数器为零，或等待中的线程中断或者等待超时。

闭锁是一次性对象，一旦进入终止状态，就不能被重置。

``` bash
package concurrent.module;

import java.util.concurrent.CountDownLatch;

/**
 * 闭锁实现统计多个线程执行时间
 */
public class TestHarness {

   public long timeTasks(int nThreads, final Runnable task) throws InterruptedException {
       CountDownLatch startGate = new CountDownLatch(1);
       CountDownLatch endGate = new CountDownLatch(nThreads);

       for (int i = 0 ; i < nThreads; i++) {
           new Thread(() -> {
               try {
                   startGate.await();
                   try {
                       task.run();
                   } finally {
                       endGate.countDown();
                   }
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
           }).start();
       }

       long startTime = System.nanoTime();
       startGate.countDown();
       endGate.await();
       long endTime = System.nanoTime();
       return endTime - startTime;
   }

    public static void main(String[] args) throws InterruptedException {
       Runnable runnable = () -> {
           for (int i = 0; i < 10000; i++) {

           }
       };
        long time = new TestHarness().timeTasks(4, runnable);
        System.out.println(time);
    }
}
```