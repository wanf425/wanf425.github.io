---
layout: article
title: 以太坊快速发币教程
key: A20180621001
tags: blockchain programming
category: blog
date: 2018-06-21 00:00:00 +08:00
modify_date: 2017-06-21 00:00:00 +08:00
---

> 不用敲代码，10分钟，不到40元，在以太坊上快速发行自己的代币。

以太坊是与比特币齐名的区块链项目。与比特币的应用场景不同，以太坊之所以能取得现在的成就，很大程度是由于它提供了一条区块链代币发行的快捷路径，任何人都可以在以太坊上以极低的代价发行自己的代币。 

<!--more-->

关于如何在以太坊上发行代币，网上已经有很多教程，但是这些教程普遍存在两大问题：

1. 在中国大陆上网的同学，需要翻墙。
2. 操作过程比较繁琐，有的还要复制智能合约代码，对于没有编程基础的同学来讲存在门槛。

这篇教程针对这些问题做了一些改进，不需要翻墙，而且操作简便，即使是编程0基础的同学也可以在10分钟内学会。

以下所有操作都是基于PC端，准备好电脑，让我们开始吧。

### 第一步，安装谷歌浏览器

能够翻墙的同学，请去官网下载：
https://www.google.cn/chrome/index.html

不能翻墙的同学，可以通过"腾讯电脑管家"、"360软件管家"之类的软件管理工具，搜索“谷歌浏览器”后下载。

不建议通过百度搜索，小心下载到病毒软件。

### 第二步，安装MetaMask插件

MetaMask是一款在谷歌浏览器上使用的以太坊钱包，该钱包不需要下载，只需要在谷歌浏览器添加扩展程序即可，非常轻量级，使用起来也很方便。

安装步骤：

1. 打开谷歌浏览器，访问网站：http://chrome-extension-downloader.com/ 。
2. 输入MetaMask扩展程序ID：nkbihfbeogaeaoehlefnkodbefgpgknn。
3. 点击“Download extension”按钮。

   ![](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-06-10 下午9.40.02.png)

4. 文件下载完成后，在浏览器地址栏输入"chrome://extensions/"，进入扩展程序管理窗口。
5. 将下载到的文件MetaMask_xxx.crx（xxx是最近的版本号）拖拽到管理窗口。
6. 点击"添加扩展程序"按钮。
![](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-06-10 下午10.40.10.png)

   添加成功后，可以在扩展程序管理窗口看到新增的MetaMask插件，同时在插件快捷栏中看到一个狐狸头像。
   ![](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-06-10 下午10.01.59.png)

### 第三步，创建钱包和账户

发行代币需要先创建钱包和账户，具体步骤：

1. 打开MetaMask（点击那个狐狸头像）。
2. 点击"Accept"按钮，点击后按钮变灰。
3. 拖动中间的文字到最底部。
4. "Accept"按钮重新变亮，再次点击按钮。
   ![](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-06-10 下午10.13.18.png)

5. 输入钱包密码，点击“create”按钮。
   ![](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-06-10 下午10.30.57.png)


6. 保存助记词。
   ![](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-06-10 下午10.33.50.png)

创建成功后可以看到下面的页面，系统已经为我们创建了Account 1账户。
![](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-06-10 下午10.42.42.png)

### 第四步，发行代币

在主网（Main Network）发行代币需要充值以太坊到账户作为发行费用，一般是0.01个以太坊（约合40元人民币）。

不过，本教程会先教大家在测试网发行代币，在这里可以申请测试用的以太坊（不用花钱哦）。

具体步骤：

1. 切换到测试网（Reposten Test NetWork）。
![](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-06-10 下午10.47.00.png)

2. 申请测试用的以太坊。点击“BUY”按钮，接着点击“REPOSTEN TEST FAUCET”按钮，自动跳转到 https://faucet.metamask.io/ 页面。
  ![](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-06-10 下午10.56.13.png)

3. 点击“request 1 ether from fauet”按钮，耐心等待1分钟。如果想要更多的以太坊，可以多点几次。
  ![](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-06-10 下午10.57.30.png)

4. 查看账户信息，到账1个以太坊。
   ![屏幕快照 2018-06-10 下午11.04.14](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-06-10 下午11.04.14.png)

5. 打开网址 http://tokenfactory.surge.sh/#/factory 创建代币。输入代币信息后点击"Create Token"按钮。
![](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-06-10 下午11.16.55.png)

6. 浏览器自动弹出交易确认页面，点击"SUBMIT"按钮。
![](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-06-10 下午11.16.45.png)

7. 提交成功后，浏览器会自动跳转到新页面，并显示代币信息。在MetaMask添加对应的地址，即可查看代币余额。
   ![](http://ot6uqhsry.bkt.clouddn.com/屏幕快照 2018-06-10 下午11.31.34.png)

至此整个过程顺利完成，怎么样，是不是觉得在以太坊上发行代币特别简单呢？

建议大家在测试网络多操作几次，等熟练了之后，就可以换到主网发行属于自己的代币了。





