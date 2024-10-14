---
title: AddTwoNumbers
date: 2020-10-23 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 题目介绍

[两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

示例：

```
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```

## 题目解法

```
package algorithm;

public class AddTwoNumbers {
    public static ListNode addTwoNumbers(ListNode l1, ListNode l2) {

        ListNode root = new ListNode(0);
        ListNode cursor = root;
        int carry = 0;
        while(l1 != null || l2 != null || carry != 0) {
            int l1Val = l1 != null ? l1.val : 0;
            int l2Val = l2 != null ? l2.val : 0;
            int sumVal = l1Val + l2Val + carry;
            carry = sumVal / 10;

            ListNode sumNode = new ListNode(sumVal % 10);
            cursor.next = sumNode;
            cursor = sumNode;

            if (l1 != null) {
                l1 = l1.next;
            }
            if (l2 != null) {
                l2 = l2.next;
            }
        }

        return root.next;
    }

    public static void main(String[] args) {
        ListNode l1 = new ListNode(2);
        ListNode cursor = l1;
        ListNode next = new ListNode(4);
        cursor.next = next;
        cursor = next;
        next = new ListNode(3);
        cursor.next = next;

        ListNode l2 = new ListNode(5);
        cursor = l2;
        next = new ListNode(6);
        cursor.next = next;
        cursor = next;
        next = new ListNode(4);
        cursor.next = next;

        ListNode listNode = addTwoNumbers(l1, l2);
        print(listNode);

    }

    private static void print(ListNode listNode) {
        do {
            System.out.println(listNode.val);
            listNode = listNode.next;
        } while (null != listNode);
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
7
0
8
```

思路：

- 链路是从个位到最高位，所以可以用最简单的加进位就可以解决。