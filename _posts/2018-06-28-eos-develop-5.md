---
layout: article
title: EOSIO开发（五）- 钱包之实战篇
key: A20180628003
tags: 编程 区块链 EOS 钱包
category: blog
date: 2018-06-28 18:33:00 +08:00
modify_date: 2017-06-28 18:33:00 +08:00
---

通过这篇文章，我们将学习如何通过cleos命令管理钱包。

<!--more-->

## 环境准备

Docker环境：

```
docker pull eosio/eos # 下载镜像
docker run --name keosd -t eosio/eos /opt/eosio/bin/keosd arg1 arg2 # 启动keosd
docker exec -it keosd /bin/bash # 进入keosd命令行界面
```
非Docker环境：

```
cd /path_to_eos/build/programs/cleos # 进入cleos编译目录
```

与钱包相关的操作全部在 ```cleos wallet``` 命令中，使用介绍如下：
![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-01 下午4.14.40.png)

## 创建钱包

通过```cleos wallet create```命令创建钱包，创建成功后将会看到下面的信息：

![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-01 下午4.16.12.png)

创建了一个名称为"default"的钱包，并且钱包的密码是PW5KX8my1cqU38eZW1PhXUADLgjUxyqBv7wTYz3WDJg5mAnREyuNb。这个密码一定要好好保存，因为它是解锁钱包的唯一凭证。

如果想要创建一个自定义名称的钱包，可以使用```cleos wallet create -n```命令。

![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-01 下午4.33.10.png)

在上图中，创建了一个名称为testwallet的钱包。

有一点要注意的是，```cleos wallet```的所有子命令，如果没有指定钱包名称，则都是默认操作default钱包，否则可以通过 -n 指定被操作的钱包。

## 查询钱包信息

每当我们创建一个钱包时，EOSIO会在本地生成生成一个钱包文件，保存在```~/eosio-wallet```目录下。

![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-01 下午4.41.11.png)

进入目录之后可以看到dfault.wallet和testwallet.wallet文件，分别对应刚刚生成的两个钱包。

打开一个文件看看，里面保存的是一些加密字符串。
![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-01 下午4.43.56.png)

我们也可以通过```cleos wallet list```命令查询钱包信息。

![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-01 下午4.45.04.png)

查询结果显示目前有default和testwallet两个钱包，注意钱包名称后面的 * 号，它表示这两个钱包目前都是解锁(unlock)状态，锁定状态的钱包后面没有 * 号。

例如，锁定testwallet钱包后，查询钱包信息。
![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-01 下午4.48.34.png)

## 钱包状态转换

钱包有三种状态：lock、unlock和close。

### 锁定钱包
前面的章节已经为大家展示了通过```cleos wallet lock -n 钱包名```命令锁定钱包，如果想锁定default钱包，可以不要 -n 参数。
![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-01 下午4.48.34.png)

锁定状态下的钱包，将不能执行任何与该钱包相关的操作，也不能使用钱包中保存的密钥。例如，保存私钥，对交易签名等。

为了安全起见，建议使用完钱包之后就将其锁定。

### 解锁钱包
通过```cleos wallet unlock -n 钱包名```命令解锁钱包，解锁时需要输入钱包密码。

![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-01 下午5.02.10.png)

### 关闭/打开钱包

当客户端keosd关闭之后，钱包也会进入关闭(close)状态，重启keosd后，需要通过```cleos wallet open```命令重新打开钱包。

重启keosd后，查询钱包信息。
![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-01 下午5.06.25.png)

查询不到任何钱包信息，说明此时所有钱包都是close状态。

使用```cleos wallet open```打开default钱包。
![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-01 下午5.08.04.png)

打开后的钱包处于锁定状态，如果想要使用钱包，还需要做解锁操作。
![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-01 下午5.09.28.png)

### 状态转换总结

通过下面的钱包状态转换图，我们可以更好的理解钱包状态之间的关系。

![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-01 下午5.44.04.png)


## 导入密钥

钱包的一个重要功能就是保存密钥（私钥和公钥），在对交易进行签名时，需要用到这些密钥。

首先，使用```cleos create key```命令生成公钥和私钥。

![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-01 下午7.04.49.png)

通过```cleos wallet import ${private_key}```命令导入私钥，在导入私钥时，钱包会自动根据私钥得到公钥，因此实际上钱包是同时保存了私钥和公钥。

![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-01 下午7.06.31.png)

最后，通过```cleos wallet keys```命令查看已导入的密钥。
![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-01 下午7.07.53.png)

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
[Tutorial Comprehensive Accounts and Wallets](https://github.com/EOSIO/eos/wiki/Tutorial-Comprehensive-Accounts-and-Wallets)


