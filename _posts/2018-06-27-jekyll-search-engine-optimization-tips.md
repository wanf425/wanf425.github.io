---
layout: article
title: Jekyll建站之搜索引擎收录小技巧
key: A20180626001
tags: 写作 编程 Jekyll 博客 SEO
category: blog
date: 2018-06-27 00:00:00 +08:00
modify_date: 2017-06-27 00:00:00 +08:00
---

当你用Jekyll辛辛苦苦搭建好了个人博客网站，兴奋的想要在谷歌上搜索自己的博客信息时，却突然发现完全没有任何记录？不止谷歌，其它搜索引擎，例如百度、雅虎等等也是一片空白，此时你是否会心生疑虑，为什么我的网站在搜索引擎中搜不到呢？

<!--more-->

想要理解原因，我们首先得明白，为什么其它的网站能被搜索引擎收录？原因是搜索引擎的爬虫程序提前抓取了这些网站的相关信息，然后收录下来供搜索使用。

想让自己的网站被收录，一个办法是被动等待爬虫访问你的网站，但是在internet浩瀚的海洋中，这犹如大海捞针，非常困难。另一个办法就是主动通知爬虫，告诉他们这里有信息希望被收录。

所以对于自建博客的我们来说，把文章发到博客上还不能算结束，我们得想办法主动提高博客被收录的几率，下面让我来介绍几个相关的小技巧。

### 技巧1：提交sitemap文件

sitemap又称站点地图，顾名思义它就像一张地图一样，记录了网站所有网页的路径信息，例如下面的例子：

```
<?xml version="1.0" encoding="UTF-8"?>
<urlset
      xmlns="http://www.sitemaps.org/schemas/sitemap/0.9"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.sitemaps.org/schemas/sitemap/0.9
            http://www.sitemaps.org/schemas/sitemap/0.9/sitemap.xsd">
<url>
  <loc>https://www.taowong.com/blog/2018/06/22/eos-develop-1.html</loc>
  <lastmod>2018-06-25T15:07:14+00:00</lastmod>
  <priority>0.80</priority>
</url>
<url>
  <loc>https://www.taowong.com/archive.html?tag=%E5%8C%BA%E5%9D%97%E9%93%BE</loc>
  <lastmod>2018-06-25T15:07:14+00:00</lastmod>
  <priority>0.80</priority>
</url>
```

通过这张地图，爬虫程序可以很方便的将网站上所有的网页信息都抓取下去。

Jekyll提供的sitemap插件 [Jekyll Sitemap Generator Plugin](https://github.com/jekyll/jekyll-sitemap) 让我们可以很方便的生成sitemap信息。除此之外，网上也有很多在线生成sitemap文件的网站，例如 [xml-sitemap.com](https://www.xml-sitemaps.com)。

生成好文件之后，我们可以通过 [Google Search Console](https://www.google.com/webmasters/tools/) 以及 [百度搜索资源平台](https://ziyuan.baidu.com/site) 分别提交给谷歌和百度。

这里需要特别说明的是，对于将代码托管在Github的同学，百度爬虫是无法抓取信息的，因为Github认为百度爬虫抓取过于频繁，将它禁掉了，短期内解禁的可能性也不大。

对于这种情况，我们有三种解决方案：

1. 无所谓，我有谷歌就够了。
2. 将代码托管在 [Coding](https://coding.net/) 平台。
3. 使用代理工具。

详细的内容就不展开说了，有兴趣的同学可以自行去研究。


### 技巧2：在页面头信息中增加keywords和description

找到页面头文件（以我自己为例，是_includes/head.html文件），在其中添加代码。

```
<meta name="description" content="{{ page.summary | escape }}">  
<meta name="keywords" content="{{ page.tags | join: ', ' | escape }}"/> 
```

其中page.summary和page.tags是遵循YAML语法定义的字段，例如下面的示例：

```
summary: How to add metadata to the Jekyll-based site: google sitemap xml, Open Graph and plain old "meta"-tags. 
tags: [jekyll,blogging,facebook,metadata]
```

这种方式的原理，是通过metadata中的keywords和description关键字，告诉来访的爬虫程序当前页面的关键信息，提高页面在搜索引擎中被匹配的概率。
      

### 技巧3：添加Open Graph Protocol（开放内容协议）

同样是在页面头文件中添加代码，例如：

```
<!-- 标题 -->
<meta property="og:title" content="Example title of article">
<!-- 网站名 -->
<meta property="og:site_name" content="example.com website">
<!-- 类型 -->
<meta property="og:type" content="article">
<!-- 页面地址 -->
<meta property="og:url" content="http://example.com/example-title-of-article">
<!-- 略缩图地址 -->
<meta property="og:image" content="http://example.com/article_thumbnail.jpg">
<!-- 页面的简单描述 -->
<meta property="og:description" content="This example article is an example of OpenGraph protocol.">
```

Open Graph Protocol（开放内容协议）是一种新的HTTP头部标记，这种协议可以让网页成为一个“富媒体对象”，通过这个协议，网页内容可以被其他社交网站网站（例如Facebook）引用，从而增加自己网站的传播力度。
  
### 小结
    
前面提供的只是一些简单的小技巧，除了sitemap，还可以通过其它方式通知搜索引擎，例如手动提交链接，或者在用户访问页面时自动发送链接信息等等。

除了自己解决收录问题，还可以找更专业的人来帮你推广，现在有很多专业做SEO（Search Engine Optimization 搜索引擎优化）的公司，只要你出得起money，没有解决不了的推广问题。

不过个人博客不需要弄那么复杂，简单维护一下就好了，自己做网站，开心最重要啦。
     
### 参考资料
[Open Graph protocol](https://en.wikipedia.org/wiki/Facebook_Platform#Open_Graph_protocol)

[Jekyll: how to add metadata to your site](http://vrepin.org/vr/JekyllMeta/)

[Github Pages + Jekyll搭建博客之SEO - Zhenyu’s Blog](http://zyzhang.github.io/blog/2012/09/03/blog-with-github-pages-and-jekyll-seo/)






