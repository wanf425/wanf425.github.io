---
layout: article
title: EOSIO开发（四）- nodeos、keosd与cleos
key: A20180628002
tags: 编程 区块链 EOS nodeos keosd cleos
category: blog
date: 2018-06-28 18:32:00 +08:00
modify_date: 2017-06-28 18:32:00 +08:00
---

前一篇文章介绍了EOSIO中钱包、账户与账户权限的概念，这一篇文章继续学习EOSIO系统的主要组件，包括nodeos、keosd以及cleos。

<!--more-->

本文执行的命令都是基于Docker环境，请先下载Docker镜像

``` 
docker pull eosio/eos 
```

## nodeos、keosd与cleos的关系

先来了解整体架构，下图展示了nodeos、keosd与cleos之间的关系。
![屏幕快照 2018-04-24 下午8.43.08](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-04-24 下午8.43.08.png)

图中有几个关键信息：
1. nodeos就是我们常说的节点，用来管理账号，上传数据到区块链。
2. keosd是钱包管理客户端，用来保存钱包信息。
3. cleos是用户（主要是开发人员）与keosd/nodeos交互的命令行工具。

## nodeos

nodeos的官方定义如下

> nodeos - server-side blockchain node component

nodeos是运行在服务端的区块链节点组件，是EOSIO系统的核心进程，可以通过它运行一个节点。

在Docker环境通过下面的命令运行一个nodeos节点。

```
docker run --name nodeos -p 8888:8888 -p 9876:9876 -t eosio/eos /opt/eosio/bin/nodeosd.sh arg1 arg2
```

这个命令有下面几层含义：
1. 使用eosio/eos镜像，启动一个名称是nodeos的容器。
2. 启动后将容器的8888端口映射到本机8888端口，9876端口映射到本机9876端口。
3. 启动容器时执行 /opt/eosio/bin/nodeosd.sh 脚本，通过此脚本运行nodeos节点。

节点启动后，我们可以看到下面的信息，节点正在生成区块数据。
![屏幕快照 2018-04-24 下午8.54.47](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-04-24 下午8.54.47.png)

使用下面的命令进入nodeos容器，并找到nodeosd.sh文件所在的文件目录。

```
docker exec -it nodeos /bin/bash
cd /opt/eosio/bin/
ls
```

![屏幕快照 2018-04-24 下午8.58.18](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-04-24 下午8.58.18.png)

可以看到在这个目录下，除了nodeos.sh，还有nodeos、keosd、cleos，这说明在eosio/eos镜像中已经包含了这三个组件的完整信息，有兴趣的同学可以继续深入了解，我在这里就不展开介绍了。

## keosd

keosd的官方定义如下

> keosd - EOSIO wallet

keosd就是EOSIO的钱包管理客户端，可以被认为是一个存储公钥-私钥的仓库，同时管理钱包信息。

nodeos与keosd之间并不存在必然关联，只有在需要签名时它们才会产生联系，例如为交易签名。

有一点要注意的是，nodeos已经包含了keosd的完整功能，也就是说在nodeos上也可以管理钱包。

在Docker环境通过下面的命令运行一个keosd客户端。

```
docker run --name keosd -t eosio/eos /opt/eosio/bin/keosd arg1 arg2
```

与运行nodeos相比，有两点不同：
1.启动keosd不需要指定端口
2.使用/opt/eosio/bin/keosd 启动keosd客户端

启动信息如下
![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-04-25 下午7.47.34.png)

使用下面的命令进入keosd容器

```
docker exec -it keosd /bin/bash
```

## cleos

cleos的官方定义如下

> cleos - command line interface to interact with the blockchain

cleos是用户与keosd/nodeos交互的命令行工具。

在nodeos或者keosd中，使用 ```cleos -h ```命令，可以查看cleos的帮助信息。

![屏幕快照 2018-04-25 下午7.53.40](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-04-25 下午7.53.40.png)

cleos 目前支持 ```version``` ```create``` ```get``` ```set``` ```trnsfer``` ```net``` ```wallet``` ```sign``` ```push``` 9个子命令。

建议大家先将所有命令翻看一遍，了解每个命令的功能范围，同时可以对照官方文档 *Command Reference* : https://github.com/EOSIO/eos/wiki/Command%20Reference 一起学习。

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

[【系列】EOS智能合约开发08 - 详解 nodeos & cleos](https://bihu.com/article/201964)

[【系列】EOS智能合约开发10 - 详解 keosd](https://bihu.com/article/211518)

[Command Reference](https://github.com/EOSIO/eos/wiki/Command%20Reference)

[Tutorial Comprehensive Accounts and Wallets](https://github.com/EOSIO/eos/wiki/Tutorial-Comprehensive-Accounts-and-Wallets)

