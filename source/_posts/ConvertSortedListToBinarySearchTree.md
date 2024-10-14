---
title: ConvertSortedListToBinarySearchTree
date: 2021-07-18 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 有序链表转换二叉搜索树

- [题目介绍](https://yangtzeshore.github.io/2021/07/18/ConvertSortedListToBinarySearchTree/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/07/18/ConvertSortedListToBinarySearchTree/#题目解法)

### 题目介绍

#### [有序链表转换二叉搜索树](https://leetcode-cn.com/problems/convert-sorted-list-to-binary-search-tree/)

给定一个单链表，其中的元素按升序排序，将其转换为高度平衡的二叉搜索树。

本题中，一个高度平衡二叉树是指一个二叉树*每个节点* 的左右两个子树的高度差的绝对值不超过 1。

**示例:**

```
给定的有序链表： [-10, -3, 0, 5, 9],

一个可能的答案是：[0, -3, 9, -10, null, 5], 它可以表示下面这个高度平衡二叉搜索树：

      0
     / \
   -3   9
   /   /
 -10  5
```

### 题目解法

```
package algorithm;

public class ConvertSortedListToBinarySearchTree {

    public static TreeNode sortedListToBST(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        return dfs(dummy, null);
    }

    private static TreeNode dfs(ListNode left, ListNode right) {
        ListNode cur = left;
        ListNode next = left;
        while (next != null && next != right) {
            cur = cur.next;

            next = next.next;
            if (next == right || next == null) {
                break;
            } else {
                next = next.next;
            }
        }

        TreeNode root = new TreeNode(cur.val);
        if (left.next != cur) {
            root.left = dfs(left, cur);
        }
        if (cur.next != right) {
            root.right = dfs(cur, right);
        }

        return root;
    }

    public static void main(String[] args) {

        ListNode n1 = new ListNode(-10);
        ListNode n2 = new ListNode(-3);
        n1.next = n2;
        ListNode n6 = n1.next;
        ListNode n3 = new ListNode(0);
        n2.next = n3;
        ListNode n4 = new ListNode(5);
        n3.next = n4;
        ListNode n5 = new ListNode(9);
        n4.next = n5;
        sortedListToBST(n1);
    }

    public static class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;

        TreeNode() {
        }

        TreeNode(int val) {
            this.val = val;
        }

        TreeNode(int val, TreeNode left, TreeNode right) {
            this.val = val;
            this.left = left;
            this.right = right;
        }
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

```

**思路：**

思路上，其实借鉴了之前的算法题。也很简单，如果是用递归的话。就是链表调试麻烦。