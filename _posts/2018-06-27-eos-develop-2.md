---
layout: article
title: EOSIO开发（二）运行合约样例
key: A20180627001
tags: 编程 区块链 EOS 智能合约
category: blog
date: 2018-06-27 23:00:00 +08:00
modify_date: 2017-06-27 23:00:00 +08:00
---

本文将介绍如何使用EOSIO自带的合约"Currency"，实现创建钱包-创建账户-执行合约 的完整流程

<!--more-->


## 前言
前一篇文章介绍了如何使用Docker搭建并运行EOSIO本地节点，本文将继续介绍如何在Docker环境下，使用系统自带的合约"Currency"，实现 **创建钱包 - 创建账户 - 执行合约** 的完整流程。

在学习文章内容之前，建议先了解EOS中Account、Permission、Active以及Action的基本概念，文末有相关的参考资料。

由于本文是在Docker环境下开发，因此部分命令与官方文档不一致（主要是文件目录地址），但是执行步骤是一致的，使用本地环境的同学可以参考官方文档的命令。

## 执行步骤

* 启动nodeos节点
* 创建钱包
* 加载Bios合约
* 创建currency账户
* 上传currency账户到区块链
* 执行智能合约

### 启动nodeos节点

下载eosio/eos镜像

```
docker pull eosio/eos
```

启动nodeos节点

```
docker run --name nodeos -p 8888:8888 -p 9876:9876 -t eosio/eos /opt/eosio/bin/nodeosd.sh arg1 arg2
```

进入nodeos节点调试界面，后续的命令都在该界面中执行。

```
docker exec -it nodeos /bin/bash
```

### 创建钱包

通过cleos wallet create命令创建钱包。

```
cleos wallet create
```

创建成功后，会生成一个随机密码。
![20180413004](http://ot6uqhsry.bkt.clouddn.com/20180413004.png)

### 加载Bios合约

将Bios合约关联到`eosio`账户上，它将使`eosio`账户获得超级管理员权限，能够直接操控其它账户的资源以及执行特殊API。

```
cleos set contract eosio ../contracts/eosio.bios -p 
```

### 创建currency账户
为账户创建OwnerKey和ActiveKey，注意 create key 命令需要执行两次。

```
cleos create key # OwnerKey
cleos create key # ActiveKey
```

这两次命令将生成两组private key和public key，分别对应OwnerKey和ActiveKey。

OwnerKey
![20180413005](http://ot6uqhsry.bkt.clouddn.com/20180413005.png)

ActiveKey
![20180413006](http://ot6uqhsry.bkt.clouddn.com/20180413006.png)

分别将两组private key导入钱包

```
cleos wallet import <private-OwnerKey>
cleos wallet import <private-ActiveKey>
```

![20180413007](http://ot6uqhsry.bkt.clouddn.com/20180413007.png)


通过 `cleos create account` 命令创建 `currency` 账户，并由`eosio` 账户为其授权。

```
cleos create account eosio currency \<public-OwnerKey> \<public-ActiveKey> 
```

![20180413008](http://ot6uqhsry.bkt.clouddn.com/20180413008.png)


验证currency账户是否创建成功。

```
cleos get account currency
```

看到下面的账户信息，则说明创建成功。
![20180413009](http://ot6uqhsry.bkt.clouddn.com/20180413009.png)


### 上传currency账户到区块链

上传之前，验证区块链中是否已经存在currency账户

```
cleos get code currency
```

如果返回hash code都为0，则说明账户不存在

![屏幕快照 2018-04-14 上午11.00.47](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-04-14 上午11.00.47.png)


上传currency账户

```
cleos set contract currency contracts/currency
```

![屏幕快照 2018-04-14 上午11.01.16](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-04-14 上午11.01.16.png)

再次验证currency账户是否已经存在

```
cleos get code currency
```

hash code不为0，上传成功。
![屏幕快照 2018-04-14 上午11.01.24](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-04-14 上午11.01.24.png)

执行currency合约的create action与issue action，

```
cleos push action currency create '{"issuer":"currency","maximum_supply":"1000000.0000 CUR","can_freeze":"0","can_recall":"0","can_whitelist":"0"}' --permission currency@active
cleos push action currency issue '{"to":"currency","quantity":"1000.0000 CUR","memo":""}' --permission currency@active
```

执行完毕后，验证currency账户中的余额(balance)是否已经正确初始化。

```
cleos get table currency currency accounts
```

可以看到balance的值为1000.0000 CUR，初始化成功。
![屏幕快照 2018-04-14 上午11.03.12](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-04-14 上午11.03.12.png)

### 执行智能合约

现在我们通过智能合约来执行一次转账操作，从currency账户转账20.0000 CUR到eosio账户。

转账之前看看两个账户的余额。

```
cleos get table currency currency accounts
cleos get table currency eosio accounts
```

currency账户的余额(balance)为1000.0000 CUR，而eosio账户没有余额。
![屏幕快照 2018-04-14 上午11.06.34](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-04-14 上午11.06.34.png)

开始转账。

```
cleos push action currency transfer '{"from":"currency","to":"eosio","quantity":"20.0000 CUR","memo":"my first transfer"}' --permission currency@active
```

转账完成后，再次查询余额，currency账户是980，eosio账户则是20，转账成功。

![屏幕快照 2018-04-14 上午11.08.07](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-04-14 上午11.08.07.png)

至此，整个流程结束。

## 小结

通过官方提供的currency合约，我们体验了 **创建钱包 - 创建账户 - 执行合约** 的整个流程，这样可以让大家对EOS合约的执行步骤有一个初步印象。

至于在执行过程当中，涉及到一些更细节的问题，比如何编写智能合约，cleos命令的具体语法以及作用，如果暂时不明白也没有关系，后续的文章我们再一步步的深入学习。

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

[EOS WIKI-Local-Environment](https://github.com/EOSIO/eos/wiki/Local-Environment#validate-the-environment---currency-contract-walkthrough)

[Accounts & Permissions](https://github.com/EOSIO/eos/wiki/Accounts%20%26%20Permissions)

[深入理解 EOS 账户权限映射](https://mp.weixin.qq.com/s?__biz=MzA4MzQ0NjAxOA==&mid=2447598010&idx=1&sn=9f193aa3f79fb4cd96c60886a5ec5170&chksm=8be191d7bc9618c19dd73b975d99f6d09291f57120c8db19a75313b4376c62bd849d0be9db5f&mpshare=1&scene=1&srcid=0414llkCfHlrMCeUMGdCWChB#rd)


