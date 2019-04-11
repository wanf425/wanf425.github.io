---
layout: article
title: 001-JAVA基础-四种引用类
key: A20190409002
tags: 编程 JAVA基础
category: blog
date: 2019-04-09 22:18:00 +08:00
modify_date: 2019-04-09 22:18:00 +08:00
---

## 定义

Java语言中，除了原始数据类型的变量，其它变量都是引用类型，指向不同的对象。

所有引用类型都继承自java.lang.ref.Reference类，并且有各自对应的Java类。

<!--more-->

## 分类

包括强引用、弱引用、软引用、虚引用四种类型。按照对象不同的可达性状态以及对垃圾回收的影响来区分。

| 引用类型 | 对象可达状态 | 垃圾回收的影响 | Java类 |
| --- | --- | --- | --- |
| 强引用 | 强可达 | 被引用对象不会被垃圾回收，除非引用被赋值为null或者超出引用的作用域 | FinalReference |
| 软引用 | 软可达 | GC时如果内存不足，被引用对象会被回收，否则不回收 | SoftReference |
| 弱引用 | 弱可达 | 每次GC时，被引用对象会被回收| WeakReference |
| 虚引用 | 虚可达 | 无法通过其访问对象，提供了一种在GC之后，做某些事情的机制| PhantomReference |

## 状态转换

下图展示了对象的生命周期以及在不同引用状态之间的转换关系。

![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/15514085395691/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-03-01%20%E4%B8%8A%E5%8D%8811.22.17.png)

## 代码示例

```
public static void main(String args[]) {

    // -----------------强引用------------------
    // 对象初始化与创建，str是一个强引用
    String str = "Reference study";

    // str赋值为null之后，对象会被回收
    // str = null;

    // -----------------软引用------------------
    // str变为软引用
    SoftReference<String> softReference = new SoftReference<String>(str);

    // 当内存不足时，等价于
    //if(JVM.内存不足) {
    //    str = null;
    //    System.gc();
    //}

    // 调用get()方法，str变回强引用
    str = softReference.get();

    // -----------------弱引用------------------
    // str变为弱引用
    WeakReference<String> weakReference = new WeakReference<String>(str);

    // 垃圾回收时，等价于
    //    str = null;
    //    System.gc();

    // 调用get()方法，str变回强引用
    str = weakReference.get();

    // -----------------虚引用------------------
    // str变为虚引用，虚引用必须与引用队列ReferenceQueue一起使用
    ReferenceQueue rq = new ReferenceQueue();
    PhantomReference<String> phantomReference = new PhantomReference<String>(str, rq);

    // 垃圾回收
    str =null;
    System.gc();

    // 回收之后 do something
    try {
        Reference r = rq.remove();
        if(r != null) {
            // do something
        }
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
```

## 各类型的使用场景

**强引用**使用范围最广，对象创建时就默认是强引用了。

**软引用**可以用来实现某些内存敏感的缓存。例如浏览器的后退按钮，后退时是从缓存读取还是重新刷新？如果存缓存，使用软引用，在内存不足时可以自动清空。

**弱引用**相比软引用，生命周期更短，主要也是在一些特殊的缓存场景中使用。

**虚引用**可以再对象finalize之后再做一些特殊的事情，例如监控对象的创建和销毁。Í

