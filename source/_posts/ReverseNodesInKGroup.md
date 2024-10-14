---
title: ReverseNodesInKGroup
date: 2021-01-28 09:22:21
tags:
- 
categories:
- Leetcode 
---



## K 个一组翻转链表

- [题目介绍](https://yangtzeshore.github.io/2021/01/28/ReverseNodesInKGroup/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/01/28/ReverseNodesInKGroup/#题目解法)

### 题目介绍

[K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

给你一个链表，每 *k* 个节点一组进行翻转，请你返回翻转后的链表。*k* 是一个正整数，它的值小于或等于链表的长度。如果节点总数不是 *k* 的整数倍，那么请将最后剩余的节点保持原有顺序。

**示例 1：**

```
给你这个链表：1->2->3->4->5

当 k = 2 时，应当返回: 2->1->4->3->5

当 k = 3 时，应当返回: 3->2->1->4->5
```

**说明：**

- 你的算法只能使用常数的额外空间。
- **你不能只是单纯的改变节点内部的值**，而是需要实际进行节点交换。

### 题目解法

```
package algorithm;

public class ReverseNodesInKGroup {

    public static ListNode reverseKGroup(ListNode head, int k) {
        if (head == null || head.next == null || k <= 1) {
            return head;
        }

        ListNode prev = head;
        ListNode cur = head;
        int length = 0;
        while (length < k && cur != null) {
            length++;
            prev = cur;
            cur = cur.next;

        }
        if (length < k){
            return head;
        }
        prev.next = null;
        ListNode left = reverse(head, k);
        ListNode right = reverseKGroup(cur, k);
        left.next = right;
		// prev此时是头结点
        return prev;
    }

    private static ListNode reverse(ListNode head, int k) {
        if (head == null || head.next == null) {
            return head;
        }

        ListNode prev = head;
        int length = 0;
        while (prev != null) {
            length++;
            prev = prev.next;
        }
        if (length < k){
            return head;
        }
        // 两两翻转
        prev = head;
        ListNode cur = prev.next;
        while (cur != null) {
            ListNode next = cur.next;
            cur.next = prev;
            prev = cur;
            cur = next;
        }
        head.next = null;
        return head;
    }

    public static void main(String[] args) {
        ListNode n1 = new ListNode(1);
        ListNode n2 = new ListNode(2);
        n1.next = n2;
        ListNode n3 = new ListNode(3);
        n2.next = n3;
        ListNode n4 = new ListNode(4);
        n3.next = n4;
        ListNode n5 = new ListNode(5);
        n4.next = n5;
        print(reverseKGroup(n1, 3));

        n1 = new ListNode(1);
        n2 = new ListNode(2);
        n1.next = n2;
        n3 = new ListNode(3);
        n2.next = n3;
        n4 = new ListNode(4);
        n3.next = n4;
        n5 = new ListNode(5);
        n4.next = n5;
        print(reverseKGroup(n1, 2));
    }

    private static void print(ListNode head) {
        while (head != null) {
            System.out.print(head.val);
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
32145
21435
```

**思路：**

这道题解法很简单，先分段反转，然后通过递归拼接。难点在于链表的指针，一不小心就指错了。