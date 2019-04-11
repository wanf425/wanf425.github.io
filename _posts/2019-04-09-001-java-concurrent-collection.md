---
layout: article
title: 001-Java基础-并发包(线程安全容器)
key: A20190409006
tags: 编程 JAVA基础
category: blog
date: 2019-04-09 22:19:06 +08:00
modify_date: 2019-04-09 22:19:06 +08:00
---

## 主要分类

Java的java.util.concurrent并发包主要提供下面四类功能。

* 线程安全容器，例如ConcurrentHashMap、CopyOnWriteArrayList、CopyOnWriteArraySet等。
* 并发队列，例如ArrayBlockingQueue、SynchronousQueue。
* Excutor线程池框架。ThreadPoolExecutor、ScheduledThreadPoolExecutor等。
* 比synchronized更高级的各种同步结构，例如CountDownLatch、Semaphore。

<!--more-->

##  线程安全容器类

包括ConcurrentHashMap、ConcurrentSkipListMap、CopyOnWriteArrayList、CopyOnWriteArraySet。

##  ConcurrentHashMap的原理

ConcurrentHashMap和ConcurrentSkipListMap是HashMap的线程安全版本，ConcurrentHashMap没有顺序，ConcurrentSkipListMap有顺序。

Java8以前，ConcurrentHashMap的实现：

* 分离锁，将内部分为多个Segment，每个Segment包含若干HashEntry数组，和HashMap类似，哈希相同的数据以链表形式存放。
* HashEntry内部使用volatile关键字保证可见性。

Java8以后，ConcurrentHashMap的实现：

* 取消Segment结构，采用与HashMap类似的散列表+链表结构，同步控制的颗粒度更小。
* 内部仍有Segment定义，但不在有任何结构上的用处，仅仅是为了兼容。
* 使用CAS等操作，在特定场景进行无锁并发操作。

##  CopyOnWriteArrayList的原理

CopyOnWriteArraySet是基于CopyOnWriteArrayList实现的，其原理与CopyOnWriteArrayList相同。

CopyOnWriteArrayList的原理是，任何修改操作(add、set、remove)，首先通过synchronized关键字加锁，然后拷贝原数组，修改后替换原数组。

例如下面的代码：


```
public boolean add(E e) {
 synchronized (lock) {
     Object[] elements = getArray();
     int len = elements.length;
           // 拷贝
     Object[] newElements = Arrays.copyOf(elements, len + 1);
     newElements[len] = e;
           // 替换
     setArray(newElements);
     return true;
            }
}
final void setArray(Object[] a) {
 array = a;
}
```

