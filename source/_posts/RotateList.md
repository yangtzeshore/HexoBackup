---
title: RotateList
date: 2021-04-13 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 旋转链表

- [题目介绍](https://yangtzeshore.github.io/2021/04/13/RotateList/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/04/13/RotateList/#题目解法)

### 题目介绍

#### [旋转链表](https://leetcode-cn.com/problems/rotate-list/)

给你一个链表的头节点 `head` ，旋转链表，将链表每个节点向右移动 `k` 个位置。

**示例1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/rotate1.jpg)

```
输入：head = [1,2,3,4,5], k = 2
输出：[4,5,1,2,3]
```

**示例2：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/roate2.jpg)

```
输入：head = [0,1,2], k = 4
输出：[2,0,1]
```

**提示：**

- 链表中节点的数目在范围 `[0, 500]` 内
- `-100 <= Node.val <= 100`
- `0 <= k <= 2 * 109`

### 题目解法

```
package algorithm;

public class RotateList {

    public static ListNode rotateRight(ListNode head, int k) {
        if (head == null || head.next == null) {
            return head;
        }

        ListNode cur = head;
        int length = 1;
        while (cur.next != null) {
            cur = cur.next;
            length++;
        }
        ListNode tail = cur;

        int position = length - k % length;
        if (position == length) {
            return head;
        }
        cur = head;
        int step = 0;
        ListNode prev = null;
        while (cur.next != null) {
            prev = cur;
            cur = cur.next;
            if (++step == position) {
                break;
            }
        }
        tail.next = head;
        prev.next = null;

        return cur;
    }

    public static void main(String[] args) {

        // 输入：head = [1,2,3,4,5], k = 2
        // 输出：[4,5,1,2,3]
        ListNode node5 = new ListNode(5, null);
        ListNode node4 = new ListNode(4, node5);
        ListNode node3 = new ListNode(3, node4);
        ListNode node2 = new ListNode(2, node3);
        ListNode node1 = new ListNode(1, node2);
        print(rotateRight(node1, 2));

        // 输入：head = [0,1,2], k = 4
        // 输出：[2,0,1]
        node3 = new ListNode(2, null);
        node2 = new ListNode(1, node3);
        node1 = new ListNode(0, node2);
        print(rotateRight(node1, 4));
    }

    private static void print(ListNode head) {
        while (head != null) {
            System.out.print(head.val + "->");
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
4->5->1->2->3->
2->0->1->
```

**思路：**

思路上，其实很简单。官方排的困难有些其实很简单，有些就很难了。