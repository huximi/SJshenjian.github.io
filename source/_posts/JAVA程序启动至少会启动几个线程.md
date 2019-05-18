---
title: JAVA程序启动至少会启动几个线程
date: 2019-01-28 17:32:01
category: 多线程
tag: [多线程,面试]
---

``` bash
	ThreadMXBean threadMXBean = ManagementFactory.getThreadMXBean();
    ThreadInfo[] threadInfos = threadMXBean.dumpAllThreads(false, false);
    for (ThreadInfo threadInfo : threadInfos) {
    	System.out.println(threadInfo.getThreadId() + ":" + threadInfo.getThreadName());
    }
```

JDK1.8 执行结果：

``` bash
	6:Monitor Ctrl-Break
	5:Attach Listener 接收外部JVM命令
	4:Signal Dispatcher 分发到不同的模块进行处理外部JVM命令，并且返回处理结结果
	3:Finalizer # 调用对象的finalize方法的线程，即垃圾回收的线程
	2:Reference Handler # 清除reference的线程
	1:main # 主线程
```

查看以上线程所属线程组及优先级

``` bash
public class Test {

    public static void main(String[] args) {
        Thread threadOne = new Thread(() -> {
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        threadOne.setName("threadOne");
        threadOne.start();

        ThreadGroup threadGroup = threadOne.getThreadGroup();
        System.out.println(threadGroup);

        ThreadGroup systemThreadGroup = threadGroup.getParent();
        System.out.println(systemThreadGroup);

        systemThreadGroup.list();
    }
}
```

执行结果：

``` bash
java.lang.ThreadGroup[name=main,maxpri=10]
java.lang.ThreadGroup[name=system,maxpri=10]
java.lang.ThreadGroup[name=system,maxpri=10]
    Thread[Reference Handler,10,system]
    Thread[Finalizer,8,system]
    Thread[Signal Dispatcher,9,system]
    Thread[Attach Listener,5,system]
    java.lang.ThreadGroup[name=main,maxpri=10]
        Thread[main,5,main]
        Thread[Monitor Ctrl-Break,5,main]
        Thread[threadOne,5,main]
```


由此看来，当一个应用程序启动时，至少创建6个线程，两个线程组(system和main线程组)，其中system线程组是main线程组的父线程组，main与Monitor Ctrl-Break处于main线程组，其他线程处于system线程组。