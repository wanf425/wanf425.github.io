---
layout: article
title: EOSIO开发（八）- 智能合约基础概念
key: A20180628006
tags: 编程 区块链 EOS  智能合约
category: blog
date: 2018-06-28 18:36:00 +08:00
modify_date: 2017-06-28 18:36 :00 +08:00
---

## 什么是智能合约

智能合约的概念早在上世纪90年代就已经被提出来，自从以太坊将其发扬光大以后，智能合约在各个区块链项目，尤其是公链中得到了广泛应用，EOS也不例外。

用一句话描述智能合约：
> 智能合约（Smart contract ）是一种旨在以信息化方式传播、验证或执行合同的计算机协议。智能合约允许在没有第三方的情况下进行可信交易，这些交易可追踪且不可逆转。

<!--more-->

例如小张和小王掷骰子，约定谁的点数大，谁就可以从对方那里赢得一块钱。

这个过程可以完全用智能合约来控制。
1. 小张先掷，合约记录点数。
2. 小王后掷，合约记录点数。
3. 合约判断点数大小，并从小点数账户扣除一块钱给大点数账户。

整个过程都由智能合约自动执行，不需要第三方参与，并且一旦结果确认了就不可反悔。

在EOS的合约样例代码中，已经实现了这个掷骰子的合约，有兴趣的同学可以先下载下来看看。
https://github.com/EOSIO/eos/tree/master/contracts/dice

## 需要掌握的背景知识

* C++
  虽然C++学起来很麻烦，但是没办法，谁让BM对C++这么青睐呢。好在开发EOSIO并不需要掌握所有的C++知识，因此我的建议是快速学习，够用就行。
  
  推荐两本书。一本是《C++语言导学》，让程序员在短时间内快速学习C++的核心知识点。另一本是《C++标准库》，可以拿来做参考书用。 
   
* Linux / Mac OS
  EOSIO目前只能运行在Linux 或者Mac OS操作系统上。
  
* 命令行基础知识
  EOSIO提供的一些工具需要通过命令行操作，比如前面文章中经常用到的cleos。

## 基础概念

### Action

一个智能合约可以定义多个Action，每个Action代表一次单独的操作，例如：转账、掷骰子、比较点数大小。

映射到代码中，可以将Action理解成类中的函数，函数中定义了调用Action时需要执行的操作。

智能合约与账户通过Action的方式进行通信。Action可以单独执行，也可以和其他Action一起作为一个整体执行。

例如：账户小王执行了【还钱】Action，【还钱】Action通知账户工商银行，执行【转账】Action，【转账】Action又通知账户【小李】，执行【短信通知】Action。

### Transaction

Transaction是一个或多个Action的集合，一个Transaction中的Action要么全部成功，要么全部失败，从这个角度来看，Transaction与数据库中的“事务”非常像。

在前面Action的例子中，【还钱】Action、【转账】Action、【短信通知】Action可以都被包含在一个事务中。

下面是包含了多个Action的Transaction例子（关键字：actions）。
 
```
{
	"expiration": "2018-04-01T15:20:44",
	"region": 0,
	"ref_block_num": 42580,
	"ref_block_prefix": 3987474256,
	"net_usage_words": 21,
	"kcpu_usage": 1000,
	"delay_sec": 0,
	"context_free_actions": [],
	"actions": [{
		"account": "eosio.token",
		"name": "issue",
		"authorization": [{
			"actor": "eosio",
			"permission": "active"
		}],
		"data": "00000000007015d640420f000000000004454f5300000000046d656d6f"
	},{
		"account": "...",
		"name": "...",
		"authorization": [{
			"actor": "...",
			"permission": "..."
		}],
		"data": "..."
	}],
	"signatures": [""],
	"context_free_data": []
}
```

### 合约交互模式 

EOSIO中提供了两种智能合约的交互模式，Inline（内联）和 Deferred（延迟）。

Inline可以被理解为实时交互模式，在一个Transaction中所有的Action，实时调用顺序执行。

Deferred可以被理解为延时交互模式，TransactionA中的部分Action没有被立即执行，而是延迟一段时间后，由TransactionB来执行。

下面用一个具体的示例来说明两种模式。

假设有一个Employer（雇主）账户，希望通过它的payroll Action将工资发放给Employees（雇员）账户。

#### Inline模式
在Inline模式下，所有的Action是被实时顺序调用的，整个执行过程的时序图如下所示。

![example-transaction-flow-diagra](http://ot6uqhsry.bkt.clouddn.com/example-transaction-flow-diagram.png)

图中```employer::runpayroll```发起了发工资的请求，
而```employer::dootherstuff```是雇主执行的其它操作，我们可以不用理会它。

* 第一步，```employer::runpayroll``` 执行inline action ```eosio::token::transfer```，从Employer账户发送给Bank账户，并将需要收到工资的Employee账户信息保存在memo字段中。

* 第二步，```eosio::token::transfer``` action修改了token的转账状态，并将memo字段的信息通知给Bank账户。

* 第三步，Bank账户部署了一个合约，用来监听```eosio::token::transfer``` action的通知。当收到通知后，根据memo字段的内容通知对应的Employee账户。

* 第四步，Employee账户同样部署了合约监听```eosio::token::transfer```action的听通知。当收到通知后，执行inline action ```bank::doacctpolicy```。

* 第五步，Bank账户执行```bank::doacctpolicy``` action。

#### Deferred模式

还是同样的场景，假设```bank::doacctpolicy``` 不是以inline模式调用，而是以deferred模式调用，时序图会改成下面这个样子。

![example-deferred-transaction-flo](http://ot6uqhsry.bkt.clouddn.com/example-deferred-transaction-flow.png)

从时序图来看，主要的区别：
1. ```bank::doacctpolicy``` action并没有立即被执行，而是放到了一个队列（queue）中。
2. ```bank::doacctpolicy``` action是在延迟一段时间以后，放在```Defered Transaction```执行的。

通过前面的描述，我们可以总结出inline模式与deferred模式的主要区别：
1. inline模式是实时执行action，而deferred模式是延时执行。
2. deferred模式下的action会被放到一个队列中，后续执行时再从队列中拿出来。
3. inline模式中的action是在同一个Transaction里，而deferred模式下action被分散到不同的Transaction里。

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

[什么是智能合约？](
http://t.cj.sina.com.cn/articles/view/6449929019/180721b3b001004wkx?cre=tianyi&mod=pcpager_fintoutiao&loc=1&r=9&doct=0&rfunc=100&tj=none&tr=9)

[EOSIO Smart Contract](
https://github.com/EOSIO/eos/wiki/Smart-Contract#eosio-smart-contract)

