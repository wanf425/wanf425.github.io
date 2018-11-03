---
layout: article
title: 常见链表操作-删除链表倒数第n个节点（JAVA实现）
key: A20181020001
tags: 编程 算法 数据结构
category: blog
date: 2018-10-20 21:01:00 +08:00
modify_date: 2018-10-20 21:01:00 +08:00
---


### 问题

给出一个单向链表，删除该链表倒数第n个节点，并返回头节点。

例如：
给出链表 1->2->3->4->5，n=2
返回链表 1->2->3->5

<!--more-->

### 解题思路

**最容易想到的算法：**
先遍历一次链表，记下链表的长度，然后计算倒数第n个节点的下标m，然后再遍历一次链表，移除第m个节点。
这种算法的时间复杂度是O(n^2 )。
有没有时间复杂度更低的算法呢？答案给是有。

**优化后的算法：**
定义两个指针p和q。
p从头节点开始，遍历n个节点，然后q从头节点开始，与p一起继续向下遍历，直到p遍历出链表。
此时q所在的节点就是倒数第n个节点了。
此算法的时间复杂度为O(n)。

为了方便理解，我画了下面两张示意图。

第一步：
![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/20181020001.png)

第二步：
![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/20181020003.png)

### 代码


```java
    public static class ListNode {
        int val;
        ListNode next;

        ListNode(int x) {
            val = x;
        }
    }
    
    public ListNode removeNthFromEnd(ListNode head, int n) {
        if (head == null || n <= 0) {
            return null;
        }

        ListNode p = head;
        ListNode q = head;

        int i = 0;
        while (i < n) {
            p = p.next;

            if (p == null) {
                head = head.next;
                return head;
            }

            i++;
        }

        while (p.next != null) {
            p = p.next;
            q = q.next;
        }

        q.next = q.next.next;
        return head;
    }
```

