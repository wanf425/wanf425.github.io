---
layout: article
title: 常见链表操作-链表中环的检测（JAVA实现）
key: A20181014001
tags: 编程 算法 数据结构
category: blog
date: 2018-10-14 16:51:00 +08:00
modify_date: 2018-10-14 16:51:00 +08:00
---

### 问题

如何检测一个单链表中是否有环，例如下图的例子。

![20181013004](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/20181013004.png)

<!--more-->

### 解决思路1：快慢指针法

这是最常见的方法。思路就是有两个指针P1和P2，同时从头结点开始往下遍历链表中的所有节点。

P1是慢指针，一次遍历一个节点。
P2是快指针，一次遍历两个节点。

如果链表中没有环，P2和P1会先后遍历完所有的节点。

如果链表中有环，P2和P1则会先后进入环中，一直循环，并一定会在在某一次遍历中相遇。

因此，只要发现P2和P1相遇了，就可以判定链表中存在环。

![20181013003](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/20181013003.png)

#### 实现代码

```java
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

    /**
     * 判断是否有环 快慢指针法
     * 
     * @param node
     * @return
     */
    public static boolean hasLoopV1(SingleNode headNode) {
        
        if(headNode == null) {
            return false;
        }
        
        SingleNode p = headNode;
        SingleNode q = headNode.next;

        // 快指针未能遍历完所有节点
        while (q != null && q.next != null) {
            p = p.next; // 遍历一个节点
            q = q.next.next; // 遍历两个个节点

            // 已到链表末尾
            if (q == null) {
                return false;
            } else if (p == q) {
                // 快慢指针相遇，存在环
                return true;
            }
        }

        return false;
    }
}
```

### 解决思路2：足迹法

顺序遍历链表中所有的节点，并将所有遍历过的节点信息保存下来。如果某个节点的信息出现了两次，则存在环。

#### 实现代码

```java
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

    // 保存足迹信息
    private static HashMap<SingleNode, Integer> nodeMap = new HashMap<>();

    /**
     * 判断是否有环 足迹法
     * 
     * @param node
     * @return
     */
    public static boolean hasLoopV2(SingleNode node, int index) {
        if (node == null || node.next == null) {
            return false;
        }

        if (nodeMap.containsKey(node)) {
            return true;
        } else {
            nodeMap.put(node, index);
            return hasLoopV2(node.next, ++index);
        }
    }
}
```

### 总结

分别从**时间复杂度**与**内存复杂度**比较两种方法。快慢指针法的时间复杂度更高，内存复杂度更低。除此之外，足迹法额外引入了其它的数据结构:散列表。

从面试的角度看，快慢指针法更符合面试的意图。而从实际工作的角度看，快慢指针法更难理解，代码也不好写，我更推荐用足迹法。

完整代码请参考：
https://github.com/wanf425/Algorithm/blob/master/src/com/wt/adt/LinkADT.java

