---
title: JAVA同步工具类之栅栏
date: 2019-02-24 20:56:42
category: 多线程
tag: 多线程
---

栅栏类似于闭锁，它能阻塞一组线程直到某个事件发生，CyclicBarrier可以使一定数量的参与方反复的在栅栏处汇集。

当线程到达栅栏处，将调用CyclicBarrier.await方法，这个方法一直阻塞直到所有线程到达栅栏处，当所有线程到达了栅栏处，栅栏将打开，所有线程被释放，而栅栏被重置以便下次使用。

**栅栏与闭锁区别**
所有线程必须全部到达栅栏处，才能继续执行；闭锁结束前，不允许线程执行，结束时，允许所有线程执行
栅栏等待线程；闭锁等待事件

``` bash
/**
 * 栅栏理解代码示例
 */
public class CyclicBarrierWorker {

    class Worker implements Runnable {

        private int id;
        private CyclicBarrier cyclicBarrier;

        public Worker(int id, CyclicBarrier cyclicBarrier) {
            this.id = id;
            this.cyclicBarrier = cyclicBarrier;
        }

        @Override
        public void run() {
            System.out.println("线程" + id + "已到达栅栏处");
            try {
                cyclicBarrier.await();
            } catch (InterruptedException e) {
                e.printStackTrace();
            } catch (BrokenBarrierException e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(10, () -> {
            System.out.println("所有线程已到达栅栏处, 2333");
        });

        for (int i = 0; i < 10; i++) {
            new Thread(new CyclicBarrierWorker().new Worker(i, cyclicBarrier)).start();
        }
    }
}
```

**运行结果**

线程0已到达栅栏处
线程4已到达栅栏处
线程3已到达栅栏处
线程2已到达栅栏处
线程1已到达栅栏处
线程6已到达栅栏处
线程8已到达栅栏处
线程7已到达栅栏处
线程5已到达栅栏处
线程9已到达栅栏处
所有线程已到达栅栏处, 2333
