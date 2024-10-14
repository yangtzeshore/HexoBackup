---
title: RemoveNthNodeFromEndOfList
date: 2021-01-23 09:22:21
tags:
- 
categories:
- Leetcode 
---



## 删除链表的倒数第 N 个结点

- [题目介绍](https://yangtzeshore.github.io/2021/01/23/RemoveNthNodeFromEndOfList/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/01/23/RemoveNthNodeFromEndOfList/#题目解法)

### 题目介绍

[删除链表的倒数第 N 个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

**进阶：**你能尝试使用一趟扫描实现吗？

**示例 1：**

![1](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/remove_ex1.jpg)

```
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
```

**示例 2：**

```
输入：head = [1], n = 1
输出：[]
```

**示例 3：**

```
输入：head = [1,2], n = 1
输出：[1]
```

**提示：**

- 链表中结点的数目为 `sz`
- `1 <= sz <= 30`
- `0 <= Node.val <= 100`
- `1 <= n <= sz`

### 题目解法

```
package algorithm;

import java.util.HashMap;
import java.util.Map;

public class RemoveNthNodeFromEndOfList {

    public static ListNode removeNthFromEnd(ListNode head, int n) {

        Map<Integer, ListNode> map = new HashMap<>();
        int index = 0;
        ListNode cur = head;
        while (cur != null) {
            map.put(index, cur);
            index++;
            cur = cur.next;
        }

        int addr = map.size() - n;
        if (addr == 0) {
            return map.get(addr).next;
        } else {
            ListNode prev = map.get(addr - 1);
            cur = map.get(addr);
            prev.next = cur.next;
            return map.get(0);
        }
    }

    public static void main(String[] args) {

        ListNode h1 = new ListNode();
        h1.val = 1;

        ListNode h2 = new ListNode();
        h2.val = 2;
        h1.next = h2;

        ListNode h3 = new ListNode();
        h3.val = 3;
        h2.next = h3;

        ListNode h4 = new ListNode();
        h4.val = 4;
        h3.next = h4;

        ListNode h5 = new ListNode();
        h5.val = 5;
        h4.next = h5;
        print(removeNthFromEnd1(h1, 2));

        h1.next = null;
        print(removeNthFromEnd1(h1, 1));

        h1.next = h2;
        h2.next = null;
        print(removeNthFromEnd1(h1, 1));
    }

    private static void print(ListNode head) {
        while (head != null) {
            System.out.print(head.val);
            head = head.next;
        }
        System.out.println();
    }
}


class ListNode {
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
```

打印：

```
1235

1
```

**思路：**

就是用存储获取位置。然后发现提交的效率不高，参考了官方的解题思路，又写了一版，这道题应该是不难的。

```
public static ListNode removeNthFromEnd1(ListNode head, int n) {

        if (n == 0) {
            return head;
        }
        if (head.next == null) {
            return null;
        }
        ListNode first = new ListNode(0, head);
        ListNode second = first;
        ListNode lamd = first;
        int count = 0;
        while (first.next != null) {
            count++;
            first = first.next;
            if (count > n) {
//                1 2 3 4 5
                second = second.next;
            }
        }
        second.next = second.next.next;

       return lamd.next;
    }
```

**思路：**

就是两个指针，一个提前n个位置去右移。