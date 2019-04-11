---
layout: article
title: 001-Java基础-volatile关键字
key: A20190409012
tags: 编程 JAVA基础
category: blog
date: 2019-04-09 22:19:12 +08:00
modify_date: 2019-04-09 22:19:11 +08:00
---

## Java内存模型

![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/15520528787631/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-03-08%20%E4%B8%8B%E5%8D%889.51.23.png)

<!-- more -->

* 所有变量都保存在主内存中
* 每个线程都有自己独立的工作内存
* 线程对变量的操作（读/写）都必须在工作内存中

例如下面的代码

```
int i = 10; 
```

java中，必须先在工作内存中更新变量i，然后再刷新到主内存，而不是直接刷新到主内存。

<!--more-->

## volatile的作用

从线程安全的可见性、原子性、有序性分析volatile的作用。

### volatile能够保证可见性

volatile能够用来修饰变量。

被volatile修饰过的变量，在任意一个线程的工作内存中被修改后，会立刻刷新到主内存。

同时通知其它线程，将其工作内存中的该变量置为失效，如果要对该变量进行操作，则需要重新从主内存中读取。

总结一下，就是volatile解决了线程安全中的可见性问题。

例如下面的代码。

```
// 线程1
boolean stop = false;
while(!stop){
    doSomething();
}

// 线程2
stop = true;
```

* 线程1先将stop=false读取到工作内存。
* 线程2修改stop=true，然后没有将值刷新到主内存。
* 线程1仍然认为stop=false，继续执行循环中的代码。

如果用volatile修改stop变量，当线程2修改stop时，会立刻将新值刷新到主内存，并通知线程1失效其工作内存的缓存，重新从主内存读取。

### volatile不能保证原子性

volatile不能保证原子性，例如下面的代码

```
public class VolatileDemo {
    public volatile int inc = 0;

    public void increase() {
        inc++;
    }

    public static void main(String[] args) {
        final VolatileDemo demo = new VolatileDemo();
        for (int i = 0; i < 10; i++) {
            new Thread() {
                public void run() {
                    for (int j = 0; j < 1000; j++)
                        demo.increase();
                }
            }.start();
        }

        // 保证所有线程都执行完
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println(demo.inc);
    }
}
```

最后的输出并不总是期望的10000，原因如下：

* 线程1从工作内存中读取inc当前值x，然后被阻塞。
* 线程2从工作内存中读取inc当前值x，并对其做加1操作。
* 线程1从阻塞中恢复，虽然inc在主内存中的值已更新，但是线程1不会再次读取。

如果想要保证原子性，通过Synchronize、Lock、AtomicInteger等方式均可以实现。

### volatile可以部分保证有序性

Java内存模型中，volatile会禁止指令重排，它确保指令重排序时不会把其后面的指令排到其之前的位置，也不会把前面的指令排到其后面。

例如下面的例子：


```
//x、y为非volatile变量
//flag为volatile变量
x = 2;        //语句1
y = 0;        //语句2
flag = true;  //语句3
x = 4;        //语句4
y = -1;       //语句5
```

由于volatile关键字的存在，语句1和语句2一定是在语句3之前执行。

而语句4和语句5一定在语句3之后执行。


