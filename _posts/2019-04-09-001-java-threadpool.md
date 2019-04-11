---
layout: article
title: 001-Java基础-并发包(线程池)
key: A20190409008
tags: 编程 JAVA基础
category: blog
date: 2019-04-09 22:19:08 +08:00
modify_date: 2019-04-09 22:19:08 +08:00
---

## 常用线程池的创建方法

使用java.util.concurrent.Excutors类，常用的几种静态方法。

* newCachedThreadPool()。短时间缓存线程并重用，适合处理大量短时间工作任务。
* newFixedThreadPool()。线程数量固定的线程池。
* newSingleThreadExecutor()。只有1个线程的线程池。
* newScheduledThreadExecutor()。具备周期调度功能的线程池。

<!--more-->

## Excutor框架

![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/15516157044004/WechatIMG532.jpeg)

* Excutor接口，将任务提交和任务执行细节解耦。
* ExcutorService接口。提供Service的管理功能，例如shutdown。另外提供更加全面的任务提交机制，例如Future和Callable。
* Excutors类。提供常用的创建ExcutorService的静态工厂方法。
* ThreadPoolExcutor、ForkJoinPool。线程池的基本实现类

## ExcutorService实现原理

![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/15516157044004/WechatIMG533.jpeg)

如上图所示，ExcutorService中主要包含工作队列以及内部线程池两个部分。

* 工作队列。负责储存用户提交的任务，使用并发包的并发队列（BlockingQueue、SynchronousQueue等）。
* 内部线程池。保持工作线程的集合，通过ThreadFactory创建。


