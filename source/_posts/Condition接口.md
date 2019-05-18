---
title: Condition接口
date: 2019-03-29 16:15:47
category: 多线程
tag: 多线程
---

## 1. Condition基本介绍

Condition接口主要与Lock接口配合使用，替代Object中监视器方法(wait、notify、notifyAll)

我们知道，Object类中的wait、notify、notifyAll必须在同步代码块(synchronized)中调用，但我们用ReentrantLock替代了synchronized,因此无法直接调用wait等

因此，为了实现这个功能，我们必须有另外一种替代机制，这就是Condition的作用

## 2. Condition接口方法与Object监视器主要方法对比

Condition|Object|作用
-|-|-
await()|wait()|造成当前线程在接到信号或被中断之前一直处于等待状态
await(long time, TimeUnit unit)|wait(long timeout)|造成当前线程在接到信号、被中断或到达指定等待时间之前一直处于等待状态
signal()|notify()|唤醒一个等待线程
signalAll()|notifyAll()|唤醒所有等待线程

类似于wait、notify、notifyAll必须在同步代码块中使用，Condition中的await、signal必须写在Lock.lock()和Lock.unlock()之间

``` bash
ReentrantLock lock=new ReentrantLock();
Condition condition = lock.newCondition();
lock.lock();
/*获取到锁之后才能调用以下方法
condition.await();
condition.signal();
condition.signalAll();*/
lock.unlock();
```

## 3. Lock与Condition实现生产者消费者

类似于wait/notify + synchronized实现生产者消费者，详见 [wait/notify使用详解](https://sjshenjian.github.io/2019/03/19/wait-notify%E4%BD%BF%E7%94%A8%E8%AF%A6%E8%A7%A3/)

``` bash
/**
 * Lock + Condition实现生产者消费者
 */
public class ProducerConsumerByCondition {

    public static void main(String[] args) {
        Queue<Integer> queue = new LinkedList<>();
        int maxSize = 10;
        final Lock lock = new ReentrantLock();
        final Condition notEmpty = lock.newCondition();
        final Condition notFull = lock.newCondition();

        Thread producer = new Thread(new ProducerByCondition(queue, maxSize, lock, notEmpty, notFull));
        producer.setName("Producer");

        Thread consumerOne = new Thread(new ConsumerByCondition(queue, maxSize, lock, notEmpty, notFull));
        consumerOne.setName("ConsumerOne");

        Thread consumerTwo = new Thread(new ConsumerByCondition(queue, maxSize, lock, notEmpty, notFull));
        consumerTwo.setName("ConsumerTwo");

        producer.start();
        consumerOne.start();
        consumerTwo.start();
    }
}

class ProducerByCondition implements Runnable {

    private Queue<Integer> queue;
    private int maxSize;
    final Lock lock;
    final Condition notEmpty;
    final Condition notFull;

    public ProducerByCondition(Queue<Integer> queue, int maxSize, Lock lock, Condition notEmpty, Condition notFull) {
        this.queue = queue;
        this.maxSize = maxSize;
        this.lock = lock;
        this.notEmpty = notEmpty;
        this.notFull = notFull;
    }

    @Override
    public void run() {
        Random random = new Random();
        while (true) {
            lock.lock();
            try {
                while (queue.size() == maxSize) {
                    try {
                        System.out.println("Queue is full, Producer " + Thread.currentThread().getName() + " waiting");
                        notFull.await();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                int value = random.nextInt();
                System.out.println("Producer " + value);
                queue.add(value);
                notEmpty.signalAll();

                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            } finally {
                lock.unlock();
            }
        }
    }
}

class ConsumerByCondition implements Runnable {

    private Queue<Integer> queue;
    private int maxSize;
    final Lock lock;
    final Condition notEmpty;
    final Condition notFull;

    public ConsumerByCondition(Queue<Integer> queue, int maxSize, Lock lock, Condition notEmpty, Condition notFull) {
        this.queue = queue;
        this.maxSize = maxSize;
        this.lock = lock;
        this.notEmpty = notEmpty;
        this.notFull = notFull;
    }

    @Override
    public void run() {
        while (true) {
            lock.lock();
            try {
                while (queue.isEmpty()) {
                    try {
                        System.out.println("Queue is empty, Consumer " + Thread.currentThread().getName() + " waiting");
                        notEmpty.await();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                System.out.println(Thread.currentThread().getName() + " consume " + queue.remove());
                notFull.signalAll();

                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            } finally {
                lock.unlock();
            }
        }
    }
}
```
