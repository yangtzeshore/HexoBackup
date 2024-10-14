---
title: SwapNodesInPairs
date: 2021-01-27 09:22:21
tags:
- 
categories:
- Leetcode 
---



## 两两交换链表中的节点

- [题目介绍](https://yangtzeshore.github.io/2021/01/27/SwapNodesInPairs/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/01/27/SwapNodesInPairs/#题目解法)

### 题目介绍

[两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

**你不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

**示例 1：**

```
输入：head = [1,2,3,4]
输出：[2,1,4,3]
```

**示例 2：**

```
输入：head = []
输出：[]
```

**示例 3：**

```
输入：head = [1]
输出：[1]
```

**提示：**

- 链表中节点的数目在范围 `[0, 100]` 内
- `0 <= Node.val <= 100`

**进阶：**你能在不修改链表节点值的情况下解决这个问题吗?（也就是说，仅修改节点本身。）

### 题目解法

```
package algorithm;

public class SwapNodesInPairs {

    public static ListNode swapPairs(ListNode head) {

        if (head == null || head.next == null) {
            return head;
        }

        ListNode left = head;
        ListNode right = head.next;
        ListNode next = right.next;
        ListNode prev = new ListNode(0, head);
        int headIndex = 0;
        while (right != null) {
            // 交换左右位置
            left.next = next;
            right.next = left;

            // 保证链表不断
            prev.next = right;
            prev = left;
            if (headIndex == 0) {
                head = right;
                headIndex ++;
            }

            if (next != null) {
                left = next;
                right = next.next;
            } else {
                break;
            }
            if (right != null) {
                next = right.next;
            }
        }

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
        print(swapPairs(n1));

        ListNode n5 = null;
        print(swapPairs(n5));

        ListNode n6 = new ListNode(1);
        print(swapPairs(n6));
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
2143

1
```

**思路：**

这道题居然没有想到可以递归，递归的代码少到可怜；然后自己写出来的是迭代，当然和官方答案比，代码量还是稍显多。