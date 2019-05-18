---
title: CopyOnWriteArrayList实现原理
date: 2019-02-23 23:52:24
category: 高并发
tag: [高并发,面试]
---

## 1. 实现原理

CopyOnWriteArrayList是线程安全且读操作无锁的ArrayList,写操作则通过创建底层数组的新副本来实现，是一种**读写分离**的并发策略。对于写操作，比如向容器中添加一个元素，首先将当前容器复制一份，然后在新副本上执行写操作，然后将原容器的引用指向新容器

## 2. 优点

读操作不加锁，性能很高，适合**读多写少**的并发场景，使用迭代器遍历时候，不会抛出ConcurrentModificationException异常

## 3. 缺点

1）内存占用问题 每次写操作都要创建底层数组的新副本，当数据量大时，内存压力较大，可能引起频繁GC
2）无法保证实时性 写和读操作作用在不同的容器上，在写做操作执行的过程中，读操作不会阻塞但是会读取到老容器的数据

## 4. 源码分析

add/remove方式使用`ReentrantLock`，操作前lock(),操作时Arrays.copyOf进行数组拷贝,操作后unlock()

``` bash
public boolean add(E e) {
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            Object[] elements = getArray();
            int len = elements.length;
            Object[] newElements = Arrays.copyOf(elements, len + 1);
            newElements[len] = e;
            setArray(newElements);
            return true;
        } finally {
            lock.unlock();
        }
    }
```

``` bash
   public E remove(int index) {
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            Object[] elements = getArray();
            int len = elements.length;
            E oldValue = get(elements, index);
            int numMoved = len - index - 1;
            if (numMoved == 0)
                setArray(Arrays.copyOf(elements, len - 1));
            else {
                Object[] newElements = new Object[len - 1];
                System.arraycopy(elements, 0, newElements, 0, index);
                System.arraycopy(elements, index + 1, newElements, index,
                                 numMoved);
                setArray(newElements);
            }
            return oldValue;
        } finally {
            lock.unlock();
        }
    }
```

读操作时无需加锁，直接读取

``` bash
private E get(Object[] a, int index) {
        return (E) a[index];
}
```