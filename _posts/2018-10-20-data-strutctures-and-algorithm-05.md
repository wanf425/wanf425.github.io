---
layout: article
title: 常见链表操作-求链表的中间节（JAVA实现）
key: A20181020002
tags: 编程 算法 数据结构
category: blog
date: 2018-10-20 21:16:00 +08:00
modify_date: 2018-10-20 21:16:00 +08:00
---

### 问题

给出任意单向链表，找出并返回该链表的中间节点。

奇数长度的链表，例如：1->2->3->4->5
返回节点 3

偶长度的链表，例如：1->2->3->4->5->6
返回节点 4

<!--more-->

### 解题思路

与链表中环的检测一样，这题同样可以使用快慢指针。

定义两个指针fast和slow。slow一次遍历一个节点，fast一次遍历两个节点，由于fast的速度是slow的两倍，所以当fast遍历完链表时，slow所处的节点就是链表的中间节点。

### 代码


```java
    public class ListNode {
        int val;
        ListNode next;

        ListNode(int x) {
            val = x;
        }
    }
    
    /**
     * 寻找链表的中间节点
     * 
     * @param node
     * @return
     */
    public static ListNode middleNode(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }

        ListNode slow = head;
        ListNode fast = head.next;

        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }

        
        return fast == null ? slow : slow.next;
    }
    
```

