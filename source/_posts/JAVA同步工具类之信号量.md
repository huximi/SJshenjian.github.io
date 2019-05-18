---
title: JAVA同步工具类之信号量
date: 2019-02-24 18:17:57
category: 多线程
tag: 多线程
---

计数信号量(Counting Semaphore)用来控制同时访问某个资源的操作数量。信号量还可以实现资源池，为容器设置边界。

Semaphore管理着一组虚拟的许可，许可的初始数量可以通过构造函数来指定。acquire获取许可，没有许可则阻塞直到有许可，release将返回一个许可给信号量。

``` bash
/**
 * 使用Semaphore为容器设置边界
 */
public class BoundedHashSet<T> {

    private final Set<T> set;
    private final Semaphore semaphore;

    public BoundedHashSet(int bound) {
        set = Collections.synchronizedSet(new HashSet<>());
        semaphore = new Semaphore(bound);
    }

    public boolean add(T e) throws InterruptedException {
        semaphore.acquire();
        boolean wasAdded = false;
        try {
            wasAdded = set.add(e);
            return wasAdded;
        } finally {
            if (!wasAdded) {
                semaphore.release();
            }
        }
    }

    public boolean remove(Object o) {
        boolean wasRemoved = set.remove(o);
        if (wasRemoved) {
            semaphore.release();
        }
        return wasRemoved;
    }
}
```