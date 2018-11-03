---
layout: article
title: EOSIO开发（三）钱包、账户与账户权限之概念篇
key: A20180628001
tags: 编程 区块链 EOS 钱包 账户 账户权限
category: blog
date: 2018-06-28 18:13:00 +08:00
modify_date: 2017-06-28 18:13:00 +08:00
---

这篇文章为大家介绍钱包（Wallet）、账户（Accounts）、账户权限（Account authorities）的概念。

<!--more-->

## 钱包 Wallet

钱包是一个本地客户端软件，有下面两个作用：

* **保存私钥**。私钥可以和一个或多个账户关联，私钥保存在钱包中，私钥对应的公钥保存在账户中。
* **对交易签名**。账户（Account）发起交易（Transactions）时，需要通过钱包客户端对交易签名。

钱包的状态：

* **锁定**。锁定状态下的钱包无法进行任何操作（导入私钥、交易签名等等），钱包信息也处理加密状态。
* **解锁**。通过创建钱包时生成的私钥解锁钱包后，可以进行基本操作，钱包信息也处于解密状态。

## 账户 Accounts

账户由一个唯一名称来标识，名称的最大长度为12个字符。账户可以是一个自然人，也可以是一个组织或者智能合约。

账户的作用是对交易签名，并将其推送到区块链，也就是说账户是EOSIO中发起交易的主体。

## 账户权限 Account authorities

账户权限由权限(permission)、权限所有者(account)、权重(weight)以及阈值(threshold)四个部分组成，例如，下图是账户wangtao的账户权限。

*wangtao account authorities*
![20180415001](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/20180415001.png)

结合这个例子，我们来一步步了解账户权限。

### 权限 permission 

EOSIO采用父子分层的权限结构，低级权限（子权限）由高级权限（父权限）派生而来，父权限拥有子权限所有的能力。子权限能做的事父权限也能做，但是反过来，父权限能做的事，子权限不一定能做。

EOSIO在账户创建时会生成owner与active两个默认权限。

* owner 是最高等级权限，拥有owner权限就意味着拥有账户的所有权，我们可以把owner理解为超级管理员权限。
* active 是owner的子权限，主要用来发送交易、投票或者进行高级别的账户修改操作。

除了默认权限，账户还可以自定义权限（例如示例中的publish权限），用于对未来账户管理进行扩展。自定义权限要么是active的子权限，要么是其它自定义权限的子权限。

### 权限拥有者 account

虽然英文名称是account，但是它前面的账户并不完全相同，我认为翻译成权限拥有者更合适。

权限拥有者可以是账户，也可以是公钥，例如bob或者EOS5EzTZZQQxdrDaJAPD9pDzGJZ5bj34HaAb8yuvjFHGWzqV25Dch。

一个权限可以被分配给一个或多个权限拥有者，当权限被分配给多个权限拥有者时，意味着通过该权限执行的动作，需要同时获得多个权限拥有者的授权才能够进行。

至于具体需要多少个授权，则取决于权重(weight)以及阈值(threshold)。

### 权重 weight

权限拥有者在权限中的重要程度，以不小于1的整数表示。

### 阈值 threshold

执行该权限的最低权重总和。例如在示例中，执行owner权限只需要唯一的公钥EOS5EzTZZQQxdrDaJAPD9pDzGJZ5bj34HaAb8yuvjFHGWzqV25Dch（以下简称公钥）授权，因为owner权限的阈值为1，而公钥的权重也为1。

执行publish权限的情况则较为复杂，由于publish权限的阈值为2，因此bob或者tom都可以单独授权。而公钥的权重为1，无法单独授权，必须要与bob或者tom中的任意一个联合授权。

## 小结

钱包、账户以及账户权限是EOSIO最基本，也是最重要的几个概念，下一篇文章，我会结合实际的代码，来进一步说明这些概念的含义。

## 系列文章

[EOSIO开发（一）使用Docker构建本地环境](https://www.taowong.com/blog/2018/06/23/eos-develop-1.html)

[EOSIO开发（二）运行合约样例](https://www.taowong.com/blog/2018/06/27/eos-develop-2.html)

[EOSIO开发（三）钱包、账户与账户权限之概念篇](https://www.taowong.com/blog/2018/06/28/eos-develop-3.html)

[EOSIO开发（四）- nodeos、keosd与cleos](https://www.taowong.com/blog/2018/06/28/eos-develop-4.html)

[EOSIO开发（五）- 钱包之实战篇](https://www.taowong.com/blog/2018/06/28/eos-develop-5.html)

[EOSIO开发（六）- 账户之实战篇](https://www.taowong.com/blog/2018/06/28/eos-develop-6.html)

[EOSIO开发（七）- 使用CLion查看EOS代码](https://www.taowong.com/blog/2018/06/28/eos-develop-7.html)

[EOSIO开发（八）- 智能合约基础概念](https://www.taowong.com/blog/2018/06/28/eos-develop-8.html)


## 参考资料

[EOS.IO 技术白皮书 v2](https://mp.weixin.qq.com/s?__biz=MzAxMjMzMDg4OA==&mid=2650539266&idx=1&sn=5ceb18eeed24891c106b64e35cd9f7d7&chksm=83bbd7e5b4cc5ef3c855820b5e2044168140238d27599b6ca5145788fe292a0cc0efe459045b&mpshare=1&scene=1&srcid=0420KbEz4lj3fmTdKkkICfHQ#rd)

[【许晓笛】深入理解 EOS 账户权限映射](https://mp.weixin.qq.com/s?__biz=MzA4MzQ0NjAxOA==&mid=2447598010&idx=1&sn=9f193aa3f79fb4cd96c60886a5ec5170&chksm=8be191d7bc9618c19dd73b975d99f6d09291f57120c8db19a75313b4376c62bd849d0be9db5f&mpshare=1&scene=1&srcid=0420xCJYe9JZkW1MVvhkLWU0#rd)

[【系列】EOS智能合约开发12 - 账户、密钥、钱包、权限，它们之间是什么关系？](https://bihu.com/article/220155) 

[Accounts & Permissions]( https://github.com/EOSIO/eos/wiki/Accounts%20%26%20Permissions)



