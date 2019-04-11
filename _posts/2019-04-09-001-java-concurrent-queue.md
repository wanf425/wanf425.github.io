---
layout: article
title: 001-Java基础-并发包(并发队列)
key: A20190409007
tags: 编程 JAVA基础
category: blog
date: 2019-04-09 22:19:07 +08:00
modify_date: 2019-04-09 22:19:07 +08:00
---

## 并发队列的整体结构

![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/15516155120435/WechatIMG518.jpeg)

Deque支持在队列的头尾进行插入、取出操作。
其它的Queue则只能在尾插入，头取出。

<!--more-->

## BlockingQueue

Blocking意味阻塞，从队列获取(take)元素时如果队列为空，线程进入等待状态，插入(put)元素到队列时，如果队列已满，线程同样进入等待状态。

BlockingQueue内部元素的大小可以是固定值(有边界)，可以是不固定值（无边界）。

例如常用的ArrayBlockingQueue就是有边界队列。

SynchronousQueue、PriorityBlockingQueue、DelayedQueue以及LinkedTransferQueue都是无边界队列。


## 实现原理

BlockingQueue都是基于锁实现的，例如ArrayBlockingQueue。

```
/** Condition for waiting takes */
private final Condition notEmpty;

/** Condition for waiting puts */
private final Condition notFull;
 
public ArrayBlockingQueue(int capacity, boolean fair) {
 if (capacity <= 0)
     throw new IllegalArgumentException();
 this.items = new Object[capacity];
 lock = new ReentrantLock(fair);
 notEmpty = lock.newCondition();
 notFull =  lock.newCondition();
}
```

而Concurrent名字开头的Queue则是基于CAS的无锁技术。

## 典型使用场景

实现生产者-消费者场景。

