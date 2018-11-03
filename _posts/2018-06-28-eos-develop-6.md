---
layout: article
title: EOSIO开发（六）- 账户之实战篇
key: A20180628004
tags: 编程 区块链 EOS 账户
category: blog
date: 2018-06-28 18:34:00 +08:00
modify_date: 2017-06-28 18:34:00 +08:00
---

本文将介绍如何创建账户，以及定义账户权限。

涉及的操作有：

* 创建账户
* 修改账户权限
* 修改账户权限

目标是创建一个myaccount账户，同时将账户权限定义为下面的形式：

<!--more-->

*myaccount account authorities*
![屏幕快照 2018-05-05 下午12.44.15](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-05 下午12.44.15.png)
注：ac1、ac2是另外两个账户。

如果对账户权限的概念不了解，请翻阅：
[EOSIO开发（三）钱包、账户与账户权限之概念篇](https://mp.weixin.qq.com/s?__biz=MzIzMjYxMjgxNQ==&mid=2247483822&idx=1&sn=16aaa9a62cd656034141fcbab16b9e61&chksm=e893770edfe4fe18b99ede59ba762e5c9f6b9b1ee8c5cb9965e9614cf8bfa9a0625efbf1cd3f#rd)。

## 环境准备

以下操作基于Docker，非Docker环境请参考eos wiki本地启动方法。

```
# 下载镜像，已有则跳过
docker pull eosio/eos 

## keosd
# 启动keosd
docker run --name keosd -t eosio/eos /opt/eosio/bin/keosd
docker exec -it keosd /bin/bash
# 创建钱包
cleos wallet create
# 生成密钥对，私钥用于导入钱包，公钥用于生成账户
cleos create key
# 导入私钥
cleos wallet import <private-key>

## nodeos
# 启动nodeos
docker run --name nodeos -p 8888:8888 -p 9876:9876 -t eosio/eos /opt/eosio/bin/nodeosd.sh
docker exec -it nodeos /bin/bash
```

## 创建账户

### 语法分析

创建账户的语法定义如下:

```
$ cleos create account ${authorizing_account} ${new_account} ${owner_key} ${active_key}
```

* ```authorizing_account``` 是为创建账户这个动作提供资金的账户。在EOS中，创建账户时需要付出一点成本，这个成本由```authorizing_account```来承担，在本文中使用默认的eosio账户。
* ```new_account``` 被创建的账户。
* ```owner_key``` 拥有```new_account```账户owner权限的公钥。
* ```active_key``` 拥有```new_account```账户active权限的公钥。

### 实际操作

接下来要在keosd远程连接nodes节点创建账户，为什么要这么做呢？有下面两个原因:

1、keosd只是一个钱包客户端，并不具备创建账户的能力。
2、```owner_key```与```active_key```必须与导入钱包的私钥相对应，否则后续账户所有需要签名的动作都无法通过权限验证。而钱包信息是保存在keosd的，nodes无法获取，因此不应该直接在nodes上创建账户。

keosd可以通过```cleos -u ip:port``` 命令远程连接到nodos节点。

进入keosd终端操作界面，执行下面的命令：

```
# 远程连接到nodeos节点，创建myaccount账户
cleos -u http://192.168.1.101:8888 create account eosio myaccount <public-key> <public-key>
```

返回结果
![屏幕快照 2018-05-05 上午11.38.18](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-05 上午11.38.18.png)

注意结果中的```eosio <= eosio::newaccount {...}```，这一段的意思是执行了通过```eosio```账户部署的```eosio```合约中的```newaccount``` action，参数是```{...}```。

继续创建ac1和ac2账户。

```
# 远程连接到nodeos节点，创建ac1账户
cleos -u http://192.168.1.101:8888 create account eosio ac1 <public-key> <public-key>
# 远程连接到nodeos节点，创建ac2账户
cleos -u http://192.168.1.101:8888 create account eosio ac2 <public-key> <public-key>
```

## 修改账户权限
### 语法分析
修改账户权限的语法定义如下:

```
$ cleos set account permission ${account} ${permission} ${authority} ${parent}
```

* ```account``` 被修改权限的账户
* ```permission``` 被修改的权限，如果存在则修改，不存在则新增。
* ```authority``` 账户权限信息，定义权限拥有者（key、account）、权重（weight）、阈值（threshold）。
  以JSON格式表示，示例：

```
{
	"threshold": "uint32_t",
	"keys": [{
		"key": "public_key",
		"weight": "uint16_t"
	}],
	"accounts": [{
		"permission": {
			"actor": "account_name",
			"permission": "permission_name"
		},
		"weight": "uint16_t"
	}]
}
```
  
  
* ```parent``` ```permission```的父权限，可选项，默认active。

### 实际操作
先查看myaccount账户目前的账户权限信息。

```
cleos -u http://192.168.1.101:8888 get account myaccount
```

可以看到myaccount账户在被创建后，默认分配了active和owner两个权限。
![屏幕快照 2018-05-05 下午12.16.03](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-05 下午12.16.03.png)

还记得文章开头的目标么，接下来我们要创建一个自定义权限cutome，以及修改active权限。

创建自定义权限cutome

```
cleos -u http://192.168.1.101:8888 set account permission myaccount custom '{"threshold" : 2, "keys" : [{"key": "EOS8EZ2yoiDvk1QsFrjtneesMM23Nk9u3v2uzxZz9iUEN7eVj2P8p","weight": "1"}], "accounts" : [{"permission":{"actor":"ac2","permission":"active"},"weight":1}]}' active
```

执行成功后再次查看账户信息

```
cleos -u http://192.168.1.101:8888 get account myaccount
```

成功的添加了custom权限。
![屏幕快照 2018-05-05 下午12.37.27](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-05 下午12.37.27.png)

修改active权限

```
cleos -u http://192.168.1.101:8888 set account permission myaccount active '{"threshold" : 2, "keys" : [{"key": "EOS8EZ2yoiDvk1QsFrjtneesMM23Nk9u3v2uzxZz9iUEN7eVj2P8p","weight": "1"}], "accounts" : [{"permission":{"actor":"ac1","permission":"active"},"weight":1}]}' owner
```

查看账户信息，active权限下添加了一个权限所有者ac1。
![屏幕快照 2018-05-05 下午12.40.09](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/屏幕快照 2018-05-05 下午12.40.09.png)

至此，目标顺利达成~

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


