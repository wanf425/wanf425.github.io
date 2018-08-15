---
layout: article
title: 自建服务器翻墙傻瓜教程
key: A20180710002
tags: shadowsocks 翻墙 VPS
category: blog
date: 2018-07-10 10:12:00 +08:00
modify_date: 2017-07-10 10:12:00 +08:00
---

## 我的经历
### 蓝灯/云梯
一开始我会选择一些收费的VPN，比如**蓝灯**和**云梯**，优点是使用简单省心，交了钱，连上服务商提供的国外服务器，就可以翻墻了。

缺点是速度和稳定性一般般，上网速度很不稳定，服务器也经常出现一次连接不上，得多次重复连接的情况。最讨厌的是遇到敏感时期，比如最近的拾酒大，服务器就开始各种抽风，甚至连不上，非常影响使用体验。

就拿我现在用的云梯来说，已经抽风了大半个月，期间完全无法使用，服务商也没有任何解释。

<!--more-->

### 穹顶穿越
云梯挂了以后，朋友推荐我使用**穹顶穿越**，价格比云梯便宜，在特殊时期也能用。

但它有一个很大的缺点：使用场景受限。因为它是一个需要配合特定浏览器才能使用的插件，换一个浏览器就不能使用，也无法让电脑上的其它软件同时翻墻。

所以穹顶穿越对我而言只是一个过渡方案，我真正的目标是**Shadowsocks**。

### Shadowsocks

当我们在天朝局域网内与外界进行信息交流时，每一个网络请求都要经过GFW的检查。GFW通过分析网络请求的数据特征，比如要访问的域名或者IP，来判断我们是否想要访问敏感信息，一旦发现便阻断请求，停止访问。

Shadowsocks的原理是境内设备与境外服务器通讯，境内想看什么内容，就告诉境外服务器，让境外服务器将内容抓取并传送回来。

在数据传输的过程中，Shadowsocks会将数据进行加密转换，变成一些没有明显特征的信息，这样GFW也就无法进行监听和拦截了。

## 提前准备
想要使用Shadowsocks翻墻，我们需要提前做好下面的准备

* **支付工具**
   支付宝，在租借境外服务器的时候用得上。
   
* **临时的翻墻工具**
   租借境外服务器需要访问境外网站，有时需要翻墻，比如注册的时候做机器人验证。
   
* **翻译软件**
   英文不好的同学，就准备一个翻译软件。不过也用太担心，网站上的单词多看几遍，猜也能猜出来是什么意思。
   
## 开始傻瓜教程
### 第一步，租境外服务器

1.1 访问 https://bwh1.net， 国内俗称搬瓦工，一个可以租借廉价VPS服务器的网站。
![](http://ot6uqhsry.bkt.clouddn.com/3.55.01.png)

1.2 注册账号，点击右上角的Register。

![](http://ot6uqhsry.bkt.clouddn.com/10535.png)

1.3 账号注册页面，注意此时要翻墻，不然验证码是显示不出来的。

![](http://ot6uqhsry.bkt.clouddn.com/10623.png)

1.4 注册完成后，点击首页右上角的Client Area 登录

![](http://ot6uqhsry.bkt.clouddn.com/11420.png)

1.5 选择可租用的服务器，点击Services -> Order New Services

![](http://ot6uqhsry.bkt.clouddn.com/11724.png)

1.6 选择服务器，配置不用管，直接选最便宜的，点击Order Now 按钮。有的服务器在订购按钮那里显示out of stock，那就是没货了。

![](http://ot6uqhsry.bkt.clouddn.com/12309.png)

1.7 选择付款信息。
付款周期(月、季度、半年、年)。
服务器位置，都是在国外，随便选一个就行。
点击Add to Cart 按钮。 

![](http://ot6uqhsry.bkt.clouddn.com/12952.png)

1.8 确认页面，点击Check out按钮

![](http://ot6uqhsry.bkt.clouddn.com/14529.png)

1.9 支付页面，支付方式选择Alipay(支付宝)，点击Complete Order按钮。

![](http://ot6uqhsry.bkt.clouddn.com/14057.png)

支付完成后，我们就拥有一个专属于自己的境外服务器了。

### 第二步，安装Shadowsocks
2.1 还是在搬瓦工网站，点击右上角的 Client Area，然后找到Services -> My Services。

![](http://ot6uqhsry.bkt.clouddn.com/15544.png)

2.2 点击 KiwiVM Control Panel 按钮，进入服务器管理页面。

![](http://ot6uqhsry.bkt.clouddn.com/15758.png)


2.3 记下服务器IP地址(IP address)，后面会用到。点击Kill按钮，让服务器停止。

![](http://ot6uqhsry.bkt.clouddn.com/24638.png)

2.4 在服务器上安装一个新系统。

![](http://ot6uqhsry.bkt.clouddn.com/20741.png)

2.5 耐心等待几分钟，安装完成之后会收到系统邮件(发送到注册时填的邮箱)。

![](http://ot6uqhsry.bkt.clouddn.com/21107.png)

2.6 一键安装Shadowsocks服务端。

![](http://ot6uqhsry.bkt.clouddn.com/21844.png)

2.7 安装完成后可以看到**服务器的加密算法**(server encryption)、**端口号**(server port)、**密码**(server password)，记下这些信息，客户端登录时会用到。

![](http://ot6uqhsry.bkt.clouddn.com/23055.png)

2.8 按照网站提示安装客户端到本地电脑，或者登录https://sourceforge.net/projects/shadowsocksgui/files/dist/ 下载更全的版本。

![](http://ot6uqhsry.bkt.clouddn.com/23439.png)

### 第三步，翻墻

3.1 以MAC OS为例，打开客户端 -> 服务器 -> 打开服务器设定

![](http://ot6uqhsry.bkt.clouddn.com/24052.png)

3.2 输入服务器信息。
**地址栏** 第一个文本框填入步骤2.3中的服务器IP地址(IP address)，第二个文本框填入步骤2.7的端口号(server port)
**加密栏** 填入步骤2.7的加密算法(server encryption)、
**密码栏** 填入步骤2.7的密码
设置完成后点击确定按钮。

![](http://ot6uqhsry.bkt.clouddn.com/24326.png)

3.3 选择刚刚设置的服务器 -> 全局模式 -> 打开Shadowsocks。

![](http://ot6uqhsry.bkt.clouddn.com/25547.png)

3.4 在浏览器中输入https://www.google.com， 如果能够正常打开页面，恭喜你，从此就可以自由的翻墻了。

![](http://ot6uqhsry.bkt.clouddn.com/3.01.09.png)



