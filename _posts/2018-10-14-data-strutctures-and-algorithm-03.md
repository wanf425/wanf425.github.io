---
layout: article
title: 常见链表操作-两个有序表的合并（JAVA实现）
key: A20181014001
tags: 编程 算法 数据结构
category: blog
date: 2018-10-14 16:51:00 +08:00
modify_date: 2018-10-14 16:51:00 +08:00
---

### 问题

将两个有序单链表A和B，合并成C，如下图。
![](https://wangtao-1256981172.cos.ap-guangzhou.myqcloud.com/20181014006.png)

### 解决思路

同时从两个链表的头节点开始遍历，比较当前节点大小，将小的节点添加到C链表中，然后遍历。

<!--more-->

### 非递归写法

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
    
    /**
     * 有序链表合并，非递归
     * 
     * @param nodeA
     * @param nodeB
     * @return
     */
    public static SingleNode<Integer> mergeV1(SingleNode<Integer> nodeA, SingleNode<Integer> nodeB) {
        if (nodeA == null) {
            return nodeB;
        } else if (nodeB == null) {
            return nodeA;
        }

        // 初始化nodeC
        SingleNode<Integer> nodeC = new SingleNode<Integer>(null);
        // 定义当前节点
        SingleNode<Integer> currentNode = nodeC;

        // 遍历A和B，直到末尾
        while (nodeA != null || nodeB != null) {
            SingleNode<Integer> nextNode = new SingleNode<Integer>(null);
            // 找出较小的节点
            if (compareNode(nodeA, nodeB) <= 0) {
                nextNode.data = nodeA.data;
                nodeA = nodeA.next;
            } else {
                nextNode.data = nodeB.data;
                nodeB = nodeB.next;
            }

            // 添加较小的节点
            currentNode.next = nextNode;
            currentNode = currentNode.next;
        }

        // 去掉没有用的头结点
        nodeC = nodeC.next;
        
        return nodeC;
    }
    
    private static int compareNode(SingleNode<Integer> node1, SingleNode<Integer> node2) {
        if (node1 == null) {
            return 1;
        } else if (node2 == null) {
            return -1;
        }

        if (node1.data == null) {
            return -1;
        } else if (node2.data == null) {
            return 1;
        }

        return node1.data.compareTo(node2.data);
    }
}
```

这种不是很简洁，尤其是最后还要去掉一个头结点，感觉很累赘。

### 递归写法

与前面重复的代码就不贴了，直接上核心代码。

```java
    /**
     * 有序链表合并，递归
     * 
     * @param nodeA
     * @param nodeB
     * @return
     */
    private static SingleNode<Integer> mergeV2(SingleNode<Integer> nodeA, SingleNode<Integer> nodeB) {
        SingleNode<Integer> result;

        if (nodeA == null) {
            return nodeB;
        } else if (nodeB == null) {
            return nodeA;
        }

        // 找出较小的节点
        if (compareNode(nodeA, nodeB) <= 0) {
            result = nodeA;
            nodeA = nodeA.next;
        } else {
            result = nodeB;
            nodeB = nodeB.next;
        }

        result.next = mergeV2(nodeA, nodeB);
        return result;
    }
```

递归的代码看起来清爽很多，看起来也比较好理解。

完整代码请参考：
https://github.com/wanf425/Algorithm/blob/master/src/com/wt/adt/LinkADT.java

