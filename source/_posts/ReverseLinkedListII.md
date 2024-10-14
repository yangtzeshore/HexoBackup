---
title: ReverseLinkedListII
date: 2021-06-24 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 反转链表 II

- [题目介绍](https://yangtzeshore.github.io/2021/06/24/ReverseLinkedListII/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/06/24/ReverseLinkedListII/#题目解法)

### 题目介绍

#### [反转链表 II](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

给你单链表的头指针 `head` 和两个整数 `left` 和 `right` ，其中 `left <= right` 。请你反转从位置 `left` 到位置 `right` 的链表节点，返回 **反转后的链表** 。

**示例1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/rev2ex2.jpg)

```
输入：head = [1,2,3,4,5], left = 2, right = 4
输出：[1,4,3,2,5]
```

**示例 2：**

```
输入：head = [5], left = 1, right = 1
输出：[5]
```

**提示：**

- 链表中节点数目为 `n`
- `1 <= n <= 500`
- `-500 <= Node.val <= 500`
- `1 <= left <= right <= n`

### 题目解法

```
package algorithm;

public class ReverseLinkedListII {

    public static ListNode reverseBetween(ListNode head, int left, int right) {

        // 设置 dummyNode 是这一类问题的一般做法
        ListNode dummyNode = new ListNode(-1);
        dummyNode.next = head;
        ListNode pre = dummyNode;
        for (int i = 0; i < left - 1; i++) {
            pre = pre.next;
        }
        ListNode cur = pre.next;
        ListNode next;
        for (int i = 0; i < right - left; i++) {
            next = cur.next;
            cur.next = next.next;
            next.next = pre.next;
            pre.next = next;
        }
        return dummyNode.next;
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

        print(reverseBetween(n1, 2, 4));

        n1 = new ListNode(5);
        print(reverseBetween(n1, 1, 1));
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

    private static void print(ListNode head) {
        while (head != null) {
            System.out.print(head.val + "->");
            head = head.next;
        }
        System.out.println();
    }
}
```

打印：

```
1->4->3->2->5->
5->
```

**思路：**

思路上，虽然想的差不多，但是做出来，还是差的很远。尤其是，如果不切断链条，可能是出现环或者不好拼接。官方给出的解法二，三个指针，不断的将后续的节点往前抛，确实很厉害。