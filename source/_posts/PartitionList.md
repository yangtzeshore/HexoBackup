---
title: PartitionList
date: 2021-06-10 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 分隔链表

- [题目介绍](https://yangtzeshore.github.io/2021/06/10/PartitionList/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/06/10/PartitionList/#题目解法)

### 题目介绍

#### [分隔链表](https://leetcode-cn.com/problems/partition-list/)

给你一个链表的头节点 `head` 和一个特定值 `x` ，请你对链表进行分隔，使得所有 **小于** `x` 的节点都出现在 **大于或等于** `x` 的节点之前。

你应当 **保留** 两个分区中每个节点的初始相对位置。

**示例1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/partition.jpg)

```
输入：head = [1,4,3,2,5,2], x = 3
输出：[1,2,2,4,3,5]
```

**示例 2：**

```
输入：head = [2,1], x = 2
输出：[1,2]
```

**提示：**

- 链表中节点的数目在范围 `[0, 200]` 内
- `-100 <= Node.val <= 100`
- `-200 <= x <= 200`

### 题目解法

```
package algorithm;

public class PartitionList {

    public static ListNode partition(ListNode head, int x) {
        ListNode small = new ListNode(0);
        ListNode smallHead = small;
        ListNode large = new ListNode(0);
        ListNode largeHead = large;
        while (head != null) {
            if (head.val < x) {
                small.next = head;
                small = small.next;
            } else {
                large.next = head;
                large = large.next;
            }
            head = head.next;
        }
        large.next = null;
        small.next = largeHead.next;
        return smallHead.next;
    }

    public static void main(String[] args) {
        ListNode node1 = new ListNode(1);
        ListNode node2 = new ListNode(4);
        node1.next = node2;
        ListNode node3 = new ListNode(3);
        node2.next = node3;
        ListNode node4 = new ListNode(2);
        node3.next = node4;
        ListNode node5 = new ListNode(5);
        node4.next = node5;
        ListNode node6 = new ListNode(2);
        node5.next = node6;
        node6.next = null;
        print(partition(node1, 3));

        node1 = new ListNode(2);
        node2 = new ListNode(1);
        node1.next = node2;
        node2.next = null;
        print(partition(node1, 2));
    }

    private static void print(ListNode head) {
        while (head != null) {
            System.out.print(head.val + " ");
            head = head.next;
        }
        System.out.println();
    }

    public static class ListNode {
        int val;
        ListNode next;

        ListNode() {
        }

        ListNode(int val) {
            this.val = val;
        }

        ListNode(int val, ListNode next) {
            this.val = val;
            this.next = next;
        }
    }
}
```

打印：

```
1 2 2 4 3 5 
1 2 
```

**思路：**

思路上，双链表实在太巧妙了。