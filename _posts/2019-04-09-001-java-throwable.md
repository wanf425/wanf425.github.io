---
layout: article
title: 001-JAVA基础-异常类
key: A20190409001
tags: 编程 JAVA基础
category: blog
date: 2019-04-09 22:17:00 +08:00
modify_date: 2019-04-09 22:17:00 +08:00
---

## 分类

Java所有异常的父类是Throwable类，主要的继承关系如下：

![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/15513654570385/WechatIMG489.jpeg)

<!--more-->

### Error

非正常情况下出现的异常，出现后会导致程序处于非正常，甚至不可用的状态。

不需要专门捕获Error，常见Error:OutOfMemoryError，StackOverflowError。

### Exceprion

出现后不会导致程序崩溃，又分为可检查(checked)与不检查(unchecked)两类。

可检查异常需要在程序中捕获并处理，例如IOException，SQLExcetion。

不检查异常不需要捕获，可以通过代码逻辑来规避，所有的RuntimeException都是可检查异常，例如NullPointException，IndexOutOfBoundsException。

## 代码书写原则

### 不要捕获类似Exception/Throwable/Error这样的通用异常

1. 不利于理解代码。
2. 可能捕获到我们不想捕获的异常。

### 不要生吞异常

例如：

```
try {

} catch(IOException e) {
   // do nothing
}
```

不利于定位问题。

### 不要直接打印异常堆栈

例如：

```
try {

} catch(IOException e) {
   e.printStackTrace();
}
```

首先，堆栈中的信息时标准信息，看了也不一定知道错误原因。其次，在分布式系统中，堆栈轨迹很难查找。

正确的做法是详细地输出到日志系统。

### Throw early,catch late

尽可能早的抛出异常，利于定位问题出现的位置。

尽可能晚的处理异常，在合适的地方处理，甚至可以自定义成checkedException后再向上抛出。

## 参考资料

极客时间 - JAVA核心技术36讲 - 《Exception和Error有什么区别》

