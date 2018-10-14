---
layout: article
title: 常见链表操作-单链表反转(JAVA实现)
key: A20181007001
tags: 编程 算法 数据结构
category: blog
date: 2018-10-07 20:56:00 +08:00
modify_date: 2018-10-07 20:56:00 +08:00
---

在技术面试中，单链表的操作经常会被问到，比如一些常见的问题：

* 单链表反转
* 链表中环的检测
* 两个有序俩表的合并
* 删除链表倒数第n个节点
* 求链表的中间节点

接下来的文章，我对这些操作的实现算法做了一些总结，具体实现的编程语言是Java。

今天第一篇，先讲讲如何实现单链表反转。
<!--more-->

### 实现思路

第一步，从头结点开始遍历链表，找到尾结点。

第二步，从尾结点开始，反向修改每个节点的next引用，直到头结点。

第三步，修改头结点的next引用为null。

### 实现代码

定义节点结构。

```java
/**
 * 链表ADT
 * 
 * @author wangtao
 * @param <T>
 */
public class LinkADT<T> {

    /**
     * 单链表节点
     * 
     * @author wangtao
     * @param <T>
     */
    private static class SingleNode<T> {
        public SingleNode<T> next;
        public T data;

        public SingleNode(T data) {
            this.data = data;
        }

        public T getNextNodeData() {
            return next != null ? next.data : null;
        }
    }
}
```

第一种实现方案，使用递归思路，易懂但是不够简洁。

```java
    /**
     * 单链表反转 version1
     * 
     * @param node
     *            当前节点
     * @param pre
     *            前一个节点
     * @return
     */
    public static SingleNode reverseV1(SingleNode node, SingleNode pre) {

        if (node == null) {
            return node;
        } else {
            // 反转后的头结点
            SingleNode headNode;

            // next为空，说明是尾节点
            if (node.next == null) {
                // 修改next引用
                node.next = pre;
                // 指定反转后的头节点
                headNode = node;
            } else {
                // 非尾节点，继续递归
                headNode = reverseV1(node.next, node);
                //
                node.next = pre;
            }

            return headNode;
        }
    }
```

验证结果。

```java
    /**
     * 打印链表信息
     * 
     * @param node
     */
    public static void printLink(SingleNode node) {
        System.out.println("current node data:" + node.data + ", next node data:" + node.getNextNodeData());

        if (node.next != null) {
            printLink(node.next);
        } else {
            System.out.println("-------------");
        }
    }

    public static void main(String[] args) {
        SingleNode<Integer> s1 = new SingleNode<>(1);
        SingleNode<Integer> s2 = new SingleNode<>(2);
        SingleNode<Integer> s3 = new SingleNode<>(3);
        SingleNode<Integer> s4 = new SingleNode<>(4);
        s1.next = s2;
        s2.next = s3;
        s3.next = s4;
        
        printLink(s1);
        SingleNode<Integer> firstNode = reverseV1(s1, null);
        printLink(firstNode);
    }
```

打印结果，链表被正确反转。

```java
current node data:1, next node data:2
current node data:2, next node data:3
current node data:3, next node data:4
current node data:4, next node data:null
-------------
current node data:4, next node data:3
current node data:3, next node data:2
current node data:2, next node data:1
current node data:1, next node data:null
-------------
```

为什么说前面的方案不够简洁呢？因为参数中有当前节点的前一个节点，例如下面的代码

```java
    SingleNode<Integer> firstNode = reverseV1(s1, null);
```

下面来看看更加简洁的第二种实现方案。

```java
    /**
     * 单链表反转 version2
     * 
     * @param node
     * @return
     */
    public static SingleNode reverseV2(SingleNode node) {
        if (node == null || node.next == null) {
            return node;
        } else {
            SingleNode headNode = reverseV2(node.next);
            node.next.next = node;
            node.next = null;
            return headNode;
        }
    }
```

重点是下面这行代码，将下一个节点的next引用修改为当前节点。
乍一看以为有bug，仔细分析之后发现没问题，如果想不明白，建议跟一次断点。

```java
node.next.next = node; 
```

### 总结

单链表反转看上去简单，但是在没有准备的情况想要写对也不容易。如果肯花时间，抽一两个小时来写一写代码，相信大家都能掌握。

完整代码请参考：
https://github.com/wanf425/Algorithm/blob/master/src/com/wt/adt/LinkADT.java

下一篇文章，我会继续总结如何实现**链表中环的检测**。

