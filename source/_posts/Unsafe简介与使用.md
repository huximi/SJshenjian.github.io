---
title: Unsafe简介与使用
date: 2019-03-25 11:24:15
category: 多线程
tag: 多线程
---

### 1. 简介

Unsafe类可以操作JAVA内存，操作的是直接内存区，没有办法通过HotSpot的GC进行垃圾回收，需要手动进行回收，所以在使用时要特别注意内存泄漏与内存溢出。

这是一个平台相关的类，在实际的开发工作中，建议不要使用

### 2. 实例化Unsafe

Unsafe类提供了一个静态方法getUnsafe()方法获取Unsafe的实例，但是如果直接调用该类，会报出SecurityException异常，因为该类只被JDK信任的类使用。
但是我们可以采用反射的方法进行实例化。

``` bash
Field field = Unsafe.class.getDeclaredField("theUnsafe");
field.setAccessible(true);
Unsafe unsafe = (Unsafe) field.get(null);
```

### 3. 为基本类型变量设值

``` bash
public class Test {

    private int index = 1;

    public static void main(String[] args) throws NoSuchFieldException, IllegalAccessException, InstantiationException {
        Field field = Unsafe.class.getDeclaredField("theUnsafe");
        field.setAccessible(true);
        Unsafe unsafe = (Unsafe) field.get(null);

        // 获取字段在内存中的偏移量
        long offset = unsafe.objectFieldOffset(Test.class.getDeclaredField("index"));

        Test test = new Test();

        unsafe.putInt(test ,offset, 100);
        System.out.println(test.index);
    }
}
```

执行结果： `100`

### 4. 创建构造函数私有的类实例

通过allocateInstance()方法，可以创建一个实例，即使该类构造函数私有，同时不需要调用初始化代码、构造函数、各种JVM检查等。

```
public class Test {

    public static void main(String[] args) throws NoSuchFieldException, IllegalAccessException, InstantiationException {
        Field field = Unsafe.class.getDeclaredField("theUnsafe");
        field.setAccessible(true);

        Unsafe unsafe = (Unsafe) field.get(null);

        Player player = (Player) unsafe.allocateInstance(Player.class);
        System.out.println(player.getAge());

        player.setAge(33);
        System.out.println(player.getAge());
    }
}

class Player {
    private int age = 23;

    private Player() {
        age = 50;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

执行结果：

``` bash
0 // 证明私有方法及构造函数未调用
33
```