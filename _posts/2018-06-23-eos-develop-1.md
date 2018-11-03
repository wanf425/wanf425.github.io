---
layout: article
title: EOSIO开发（一）使用Docker构建本地环境
key: A20180623001
tags: 编程 区块链 EOS Docker
category: blog
date: 2018-06-23 12:00:00 +08:00
modify_date: 2017-06-23 12:00:00 +08:00
---

## 前言
一直想学习EOS开发，但是不知道怎么入门。最近从GitHub上下载了源码，发现官方已经提供了完整的[EOSIO开发入门教程](https://github.com/EOSIO/eos/wiki)，既然如此赶紧开始行动。今天是系列文章的第一篇，介绍如何使用Docker搭建本地环境。

<!--more-->

## 选择构建方式
官方支持两种方式搭建本地环境

* 使用源码
* 使用Docker

我个人首选Docker，因为可以将与EOSIO相关的软件、环境都封装在一个镜像中，不管是出了问题要回退，还是学完以后删除相关的软件，Docker都很方便。

所以在这篇文章中我就不介绍怎么使用源码构建了，有兴趣的同学可以参考官方文档的[Building EOSIO](https://github.com/EOSIO/eos/wiki/Local-Environment#2-building-eosio)章节。

## 前期准备

* 下载源码(https://github.com/EOSIO/eos.git)
* 下载Docker(17.05或者以上版本)
* 下载docker-compose(1.10.0或者以上版本)
* 打开EOSIO Readme文件(https://github.com/EOSIO/eos/blob/master/Docker/README.md)

## EOSIO的构成

在正式开始构建之前，我们先来了解EOSIO的组成部分，官方文档对于EOSIO是这么介绍的。

> EOSIO comes with a number of programs(EOSIO由下面的程序构成):

> * nodeos - server-side blockchain node component(服务端的区块链节点组件)
> * cleos - command line interface to interact with the blockchain(与区块链进行交互的命令行界面)
> * keosd - EOSIO wallet(EOSIO钱包)
> * eosio-launcher - application to assist with deploying a multi-node blockchain network(用来协助部署一个多节点区块链网络的应用程序)

中文是我自己翻译的，请原谅我的学渣翻译水平，由于对EOSIO不了解，所以只能按照字面意思硬译。翻译完之后，我发现除了keosd(EOSIO钱包)，其它几个程序的作用仍然不明所以。

好在今天的任务是搭建环境，能够将这几个程序安装起来并且正常运行就可以了，各个程序的具体作用后面再做深入学习。

## 开始构建

参考开发文档，执行下面的命令，如果源码已下载可忽略第一行。

> git clone https://github.com/EOSIO/eos.git --recursive
> cd eos/Docker
> docker build . -t eosio/eos

开始执行以后不需要人工干预，让机器自动运行就可以了，整个过程会持续半小时到一小时。

在执行的过程当中，我们来分析一下构建的步骤，打开eos/Docker/DockerFile 文件，可以看到下面的内容。

> FROM eosio/builder as builder

> RUN git clone -b master --depth 1 https://github.com/EOSIO/eos.git --recursive \
>     && cd eos \
>     && cmake -H. -B"/tmp/build" -GNinja -DCMAKE_BUILD_TYPE=Release -DWASM_ROOT=/opt/wasm -DCMAKE_CXX_COMPILER=clang++ \
>        -DCMAKE_C_COMPILER=clang -DCMAKE_INSTALL_PREFIX=/tmp/build  -DSecp256k1_ROOT_DIR=/usr/local \
>     && cmake --build /tmp/build --target install

> FROM ubuntu:16.04

> RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get -y install openssl && rm -rf /var/lib/apt/lists/*
> COPY --from=builder /usr/local/lib/* /usr/local/lib/
> COPY --from=builder /tmp/build/bin /opt/eosio/bin
> COPY --from=builder /tmp/build/contracts /contracts
> COPY --from=builder /eos/Docker/config.ini /eos/genesis.json /
> COPY nodeosd.sh /opt/eosio/bin/nodeosd.sh
> ENV EOSIO_ROOT=/opt/eosio
> RUN chmod +x /opt/eosio/bin/nodeosd.sh
> ENV LD_LIBRARY_PATH /usr/local/lib
> VOLUME /opt/eosio/bin/data-dir
> ENV PATH /opt/eosio/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

整个构建过程可以分成下面几个步骤：

1. 下载eosio/builder镜像。这个镜像包含了EOSIO运行时需要的各种软件和配置信息。
2. 编译生成可执行文件并安装。这一步与直接使用源码构建环境是一样的，环境参数可能会有细微差别。
3. 下载ubuntu镜像。看来Docker环境使用的是Ubuntu系统16.04版本。
4. 安装软件，拷贝配置信息，设置环境变量等等。

## 构建失败

正当我以为构建会很顺利的时候，命令行上出现一条错误信息：
![0406001](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/0406001.png)

在执行"COPY --from=builder /eos/Docker/config.ini /eos/genesis.json /" 语句时出错，eos/genesis.json文件不存在。

这就奇怪了，难道官方开发人员自己没有跑过构建脚本？还是我本地环境的原因导致的错误？

我尝试着将DockerFile中的"eos/genesis.json / "替换成"eos/genesis.json"，因为结尾那个多余的 / 看着非常可疑，与前面的地址中间隔了一个空格。

修改之后没有报错，构建成功并且生成了镜像，但是在启动镜像时出现异常，此法不通。

我又另外尝试了几个方法，仍然无法生成正确的镜像，无奈之下，我去GitHub上提交了一个Bug，看看官方开发人员怎么说。

![0406002](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/0406002.png)

然并卵，只有两个同病相怜的小伙伴说跟我遇到了一样的问题，官方开发人员并没有答复。

![0406003](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/0406003.png)

生成不了正确的镜像，难道我的EOS开发之旅要就此夭折？

当然不会，否则我也没必要写今天这篇文章了，正所谓条条大路通罗马，自己生成不了镜像，就拿官方已经生成好的。

## 下载eosio/eos镜像

还记得前面执行的命令么

> docker build . -t eosio/eos

这句话的意思是让我们生成一个名为eosio/eos的镜像文件，在Docker的命名习惯下，eosio/eos对应了Docker公共仓库eosio命名空间下的eos仓库。也就是说，EOS官方很有可能已经把编译好的镜像文件上传到Docker公共仓库中。

事实是否如此？我尝试做了以下操作。

**1.删除本地异常的eosio/eos镜像**
>docker rmi e7bc2acf31bf

![0407001](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/0407001.png)

**2.下载eosio/eos镜像**
>docker pull eosio/eos

![0407002](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/0407002.png)
还真让我下载到了。

## 环境验证

下载好了镜像，我们来进一步验证该镜像是否可用，不管是自己生成的镜像，还是从Docker仓库下载的，下面的验证步骤都是一样的。

参考开发文档[Start nodeos docker container only](https://github.com/EOSIO/eos/blob/master/Docker/README.md#start-nodeos-docker-container-only)章节。

**1.启动一个nodeos节点**
  
> docker run --name nodeos -p 8888:8888 -p 9876:9876 -t eosio/eos nodeosd.sh arg1 arg2
   
![0407003](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/0407003.png)
启动成功

**2.验证nodeos节点是否可用**
   
> curl http://127.0.0.1:8888/v1/chain/get_info

![0407004](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/0407004.png)
验证通过

**3.同时启动nodeos和kesod节点**

> docker volume create --name=nodeos-data-volume
> docker volume create --name=keosd-data-volume
> docker-compose up -d

![0407005](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/0407005.png)

这一步要注意几点：

* 执行命令前先把第1步启动的nodeos节点停掉
* docker-compose命令相关的配置信息在/eos/Docker/docker-compose.yml文件中
* 官方文档是说启动nodeos和kesod两个节点，但实际上还启动了builder节点，经过测试这个节点是没有用的，大家可以从docker-compose.yml文件中将builder节点相关的配置信息删掉。

**4.执行cleos命令**

>alias cleos='docker-compose exec keosd /opt/eosio/bin/cleos -H nodeosd'
cleos get info
cleos get account inita

![0407007](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/0407007.png)

注意head_block_num的值时10239，在前面**验证nodeos节点是否可用**时，返回的head_block_num是711，这说明nodeos节点已经在自动生成区块了。

至此环境验证完毕，部署成功。

## 小结

整个环境构建过程并不复杂，开发文档已经写得比较清楚，顺利的话1到2个小时可以完成。

不过大家还是要注意随机应变，不能完全依赖开发文档，比如当我遇到生成镜像失败的问题时，直接从Docker仓库下载镜像，这一点EOS的开发文档中是没有的。

另外还有一点要注意，开发文档的内容变动很频繁。我刚开始看文档的时候，还是在 https://github.com/EOSIO/eos 里面，不知什么时候开始就变到 https://github.com/EOSIO/eos/wiki 里面，目录结构也变了。

最后还是要吐槽一下EOS官方开发人员

* 既然可以直接下载镜像，干嘛还让我们自己构建，太浪费时间。
* 让我们自己构建就算了，能不能测一下构建脚本，没有BUG了再上传？

今天中午在GitHub看到昨天提交的BUG已经Close，官方开发人员的回复是：已经删除genesis.json文件。我还没有测试，希望这次修改之后不会再有新的问题。

![0407006](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/0407006.png)

## 系列文章

[EOSIO开发（一）使用Docker构建本地环境](https://www.taowong.com/blog/2018/06/23/eos-develop-1.html)

[EOSIO开发（二）运行合约样例](https://www.taowong.com/blog/2018/06/27/eos-develop-2.html)

[EOSIO开发（三）钱包、账户与账户权限之概念篇](https://www.taowong.com/blog/2018/06/28/eos-develop-3.html)

[EOSIO开发（四）- nodeos、keosd与cleos](https://www.taowong.com/blog/2018/06/28/eos-develop-4.html)

[EOSIO开发（五）- 钱包之实战篇](https://www.taowong.com/blog/2018/06/28/eos-develop-5.html)

[EOSIO开发（六）- 账户之实战篇](https://www.taowong.com/blog/2018/06/28/eos-develop-6.html)

[EOSIO开发（七）- 使用CLion查看EOS代码](https://www.taowong.com/blog/2018/06/28/eos-develop-7.html)

[EOSIO开发（八）- 智能合约基础概念](https://www.taowong.com/blog/2018/06/28/eos-develop-8.html)


