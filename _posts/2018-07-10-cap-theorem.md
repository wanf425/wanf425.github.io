---
layout: article
title: 分布式系统设计-CAP定理
key: A20180710004
tags: 编程 CAP 分布式系统
category: blog
date: 2018-07-10 10:14:00 +08:00
modify_date: 2017-07-10 10:14:00 +08:00
---

## CAP的定义

CAP定理（CAP theorem）又被称作布鲁尔定理（Brewer's theorem），是分布式系统设计中最基础、最重要的理论，它的意思是对于一个分布式计算系统来说，不可能同时满足以下三点条件：

* 一致性（Consistency）：每次读取要么获得最近写入的数据，要么获得一个错误。
* 可用性（Availability）：每次请求都能获得一个(非错误)响应，但不保证获得的数据为最新数据。
* 分区容错性（Partition tolerance）：尽管任意数量的消息被节点间的网络丢失（或延迟），系统仍继续运行。

这三个条件的核心都是**数据**，数据是否一致，数据是否可用，数据是否做了分区容错。

<!--more-->

为了更好的理解CAP原理，下面我来为大家介绍各个条件实际的应用场景。

* 一致性
银行系统部署了N个节点，其中一个节点收到了用户的转账请求，在该请求处理的过程中，以及处理结束后，无论访问银行系统的哪个节点，查询到该用户的账户金额都应该是一致的。
需要注意的是，CAP的一致性与ACID的一致并不相同，CAP侧重于不同系统节点的数据一致，ACID则侧重于数据库事务的完整性。

* 可用性
与一致性不同，可用性只要能正常响应请求就可以了，哪怕该请求依赖的数据不是最新的也不要紧。
比如一个卖书网站，用户A在某一时刻看到的库存是1，此时用户B先买了最后一本书，实际库存变成0，但是用户A不知道，也做了下单操作。
如果是一个强调一致性的系统，此时会告诉用户：库存不足，下单失败。而一个强调可用性的系统，则会让用户下单成功，然后通过其它办法补充库存。

* 分区容错性
在不同节点上保存相同的数据副本，当一个节点的数据发生变更时，通知其它节点也做相同的数据变更。

## 为什么CAP不能同时满足？

* 满足CA（一致性 + 可用性）的系统
这样的系统强调一致性和可用性，如果要做P（分区容错），就需要将数据在不同节点之间同步，但是同步是需要时间的，在同步还没有完成的时候各个节点的数据并不一致，为了满足一致性C，需要将数据未同步的节点变为暂时的不可用状态，但这样做就无法保证可用性A。
反过来讲，如果让未同步数据的节点可用，C就得不到满足，真是鱼与熊掌不可兼得。
因此对于一个CA系统，最常见的做法是数据不分区，都保存在一起。

* 满足CP（一致性 + 分区容错性）的系统
在前面例子中已经说到，如果要满足CP，需要将未同步到最新数据的节点变为不可用状态，因此无法同时兼容A。

* 满足AP（可用性 + 分区容错性）的系统
参考前面的结论：
有了CA不能保证P。
有了CP不能保证A。
因此AP系统不能兼容C。

## CAP定理的意义
了解这么绕的一个定理，对于我们设计分布式系统有什么作用呢？我个人认为，掌握CAP定理，能够让我们认识到对于分布式系统而言，出现故障时在所难免的，我们不可能构建一个完全不出故障的系统。

相反的，我们可以换一个思路，考虑在出现故障时如何能够维持系统的正常运转，结合系统的实际运行场景，在C、A、P三个条件进行适当的取舍。

