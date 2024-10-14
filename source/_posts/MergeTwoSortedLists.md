---
title: MergeTwoSortedLists
date: 2021-01-24 09:22:21
tags:
- 
categories:
- Leetcode 
---



## 合并两个有序链表

- [题目介绍](https://yangtzeshore.github.io/2021/01/24/MergeTwoSortedLists/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/01/24/MergeTwoSortedLists/#题目解法)

### 题目介绍

[合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

**示例 1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/merge_ex1.jpg)

```
输入：l1 = [1,2,4], l2 = [1,3,4]
输出：[1,1,2,3,4,4]
```

**示例 2：**

```
输入：l1 = [], l2 = []
输出：[]
```

**示例 3：**

```
输入：l1 = [], l2 = [0]
输出：[0]
```

**提示：**

- 两个链表的节点数目范围是 `[0, 50]`
- `-100 <= Node.val <= 100`
- `l1` 和 `l2` 均按 **非递减顺序** 排列

### 题目解法

```
package algorithm;

public class MergeTwoSortedLists {

    public static ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) {
            return l2;
        }
        if (l2 == null) {
            return l1;
        }

        ListNode head;
        if (l1.val <= l2.val) {
            head = l1;
            l1 = l1.next;
        } else {
            head = l2;
            l2 = l2.next;
        }
        ListNode cur = head;
        while (l1 != null && l2 != null) {
            if (l1.val <= l2.val) {
                cur.next = l1;
                cur = cur.next;
                l1 = l1.next;
            } else {
                cur.next = l2;
                cur = cur.next;
                l2 = l2.next;
            }
        }
        if (l1 == null) {
            cur.next = l2;
        }
        if (l2 == null) {
            cur.next = l1;
        }

        return head;
    }

    public static void main(String[] args) {

        ListNode node1 = new ListNode(1);
        ListNode node2 = new ListNode(2);
        node1.next = node2;
        ListNode node3 = new ListNode(4);
        node2.next = node3;

        ListNode node4 = new ListNode(1);
        ListNode node5 = new ListNode(3);
        node4.next = node5;
        ListNode node6 = new ListNode(4);
        node5.next = node6;

        print(mergeTwoLists(node1, node4));

        node1 = null;
        node2 = null;
        print(mergeTwoLists(node1, node2));

        node1 = null;
        node2 = new ListNode(0);
        node2.next = null;
        print(mergeTwoLists(node1, node2));

    }

    private static void print(ListNode node) {
        while (node != null) {
            System.out.print(node.val);
            node = node.next;
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
112344

0
```

**思路：**

很简单，就是比较大小，顺序延展下去。