---
layout: article
title: Internal server error 500 问题解决思路
key: A20180707001
tags: 编程 HTTP状态码 500
category: blog
date: 2018-07-07 17:45:00 +08:00
modify_date: 2017-07-07 17:45:00 +08:00
---

我们系统在一次升级之后，生产环境大量出现Internal server error 500错误，具体场景：

在APP上使用拍照功能后，APP通过Http协议上传压缩后的照片到服务端，但是上传过程中大量出现Internal server error 500错误，很多照片都传不上去。

经过一番排查之后，我们最终成功解决了这个问题，最后的原因有些出乎意料，这里卖个关子先不说出来。下面是我们解决问题的整体步骤以及思路。

<!--more-->

### 明确错误含义

首先明确这个错误的含义，参考[HTTP状态码](https://zh.wikipedia.org/wiki/HTTP状态码)的描述。

> **500 Internal Server Error**
> 
> 通用错误消息，服务器遇到了一个未曾预料的状况，导致了它无法完成对请求的处理。没有给出具体错误信息。

根据这个描述，基本可以排除客户端以及网络因素，需要重点关注服务端的状态。

我们系统服务端的架构如下图： 

![](http://ot6uqhsry.bkt.clouddn.com/20180707001.png)

接下来就要根据这个架构由前往后一层一层排查。

### 检查HAProxy

重点排查HAProxy当前是否可用，负荷是否超标，包括下面的一些指标。

| 排查项 | 结果 |
| --- | --- | 
| CPU是否正常 | 正常 |  
| 内存是否正常 | 正常 | 
| 线程数是否超过配置上限 | 正常 | 
| 连接数是否超过配置上限 | 正常 | 

排查之后发现一切正常，与版本更新前的数据作比较，也没有出现大幅度波动。

而在查看请求日志时，发现大量500错误信息，说明HAProxy出异常的可能性较小，错误更可能来自HAProxy之后的环节。

### 检查Jetty

重点排查jetty的配置信息。

| 排查项 | 结果 |
| --- | --- | 
| 配置是否有变动 | 正常 |  
| 应用占用的线程数是否超过上限 | 正常 | 
| 应用占用的线程数是否超过上限 | 正常 | 

虽然jetty配置信息检查正常，但是在access.log中存在大量500错误，定位到这里，有两种可能的原因：

1. 应用代码逻辑问题。部分异常信息没有被拦截住，直接抛给Jetty，导致500错误。
2. Jetty逻辑问题。请求没有到达应用，而是由于Jetty自身的某些逻辑导致请求被直接返回了。

### 检查应用

为了验证是否第一个原因，我们继续走查了应用代码，发现所有的异常都被正确处理了，不存在继续往上抛的情况。另外，也检查了图片保存的代码，确认文件连接都正确释放了。

因此，由于应用逻辑问题导致错误的可能性很小，那么第二个原因的嫌疑最大，就是Jetty逻辑问题。

如果直接排查Jetty的源码，太费时费力，这个时候最好的办法是实时抓包，看看Jetty和应用服务之间到底发生了什么。

### 使用tcpdump抓包

使用tcpdump命令抓取从jetty到应用服务之间所有的数据包，将结果输出到临时文件中。

```
tcpdump -i eth0:0 -s0 host 1X.XXX.XXX.XX -w /tmp/out1.cap
```

使用wireshark打开out1.cap文件，查找出现500错误的数据包，然后很意外的看到了下面的逻辑。

![](http://ot6uqhsry.bkt.clouddn.com/image001.png)

通过这段代码我们发现，jetty对于请求数据的大小做了限制，超过200000 byte的时候就会报错，返回错误码500。

App这次更新后，上传了很多大于200000 byte的图片，于是便出现了大量的500错误。

找到问题根源，修正起来就很简单了，在WEB-INF目录下添加jetty-web.xml 文件解决，文件内容如下：


```
<?xml version="1.0"?>

<!DOCTYPE Configure PUBLIC "-//MortBay Consulting//DTD Configure//EN"

"http://jetty.mortbay.org/configure.dtd">

<Configure id="WebAppContext"class="org.eclipse.jetty.webapp.WebAppContext">

<Set name="maxFormContentSize"type="int"> 0 </Set>

</Configure>

```

### 总结

出现**Internal server error 500**错误，往往意味着服务端出现一些未知异常，但是在排查的时候我们不能仅仅只是关注应用服务，而是要关注从服务端接收请求开始，一直到应用服务的整条链路。

以本文中的情景为例，就是从HAProxy 到 Jetty 再到 应用服务，中间的任何一个环节都有可能导致500错误。

另外，其实在一开始我们就可以采用抓包的方式去排查，因为在包数据中包含了完整请求/响应消息，比查看CPU、线程、配置信息要更加快捷，直接。

