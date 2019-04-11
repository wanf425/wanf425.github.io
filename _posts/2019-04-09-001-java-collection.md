---
layout: article
title: 001-Java基础-集合
key: A20190409004
tags: 编程 JAVA基础
category: blog
date: 2019-04-09 22:19:04 +08:00
modify_date: 2019-04-09 22:19:04 +08:00
---

## 集合框架整体设计

![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/15514430979919/WechatIMG337.jpeg)

Collection是所有集合的父类，下面主要分为List、Set、Queue三大类。

Map和java.util.concurrent中的线程安全容器并没有放入算进来。

<!--more-->

## 主要容器的数据结构与特性

* ArrayList通过双向数组实现，可以快速通过下标访问元素，但是插入、删除较慢。
* LinkedList通过双向链表实现，插入、删除较快，可以通过下标访问元素，但与ArrayList不同的是，LinkedList访问时需要遍历链表，因此速度更慢。
* TreeSet通过红黑树实现，支持自然顺序与指定规则排序。
* HashSet基于HashMap实现，数据结构是数组+链表。
* LinkedHashSet集成自HashSet，内部构建了一个记录插入顺序的双向链表。

## 并发

以上集合都不是线程安全的，Java在java.util.concurrent包中提供了专门的线程安全容器。

也可以通过Collections.synchronizedCollection()方法获得一个线程安全的集合。

这个方法的实现原理比较简单粗暴，就是在get、set、add等方法上添加synchronized。

## 集合排序默认算法

Java会根据集合中的数据类型，数据量等因素选择不同的排序算法。

* 原始数据类型，双轴快速排序。
* 对象数据类型，TimSort算法，一种归并和二分插入排序结合的算法。

