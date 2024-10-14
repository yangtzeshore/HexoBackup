---
title: RemoveDuplicatesFromSortedListII
date: 2021-06-01 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 删除排序链表中的重复元素 II

- [题目介绍](https://yangtzeshore.github.io/2021/06/01/RemoveDuplicatesFromSortedListII/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/06/01/RemoveDuplicatesFromSortedListII/#题目解法)

### 题目介绍

#### [删除排序链表中的重复元素 II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

存在一个按升序排列的链表，给你这个链表的头节点 `head` ，请你删除链表中所有存在数字重复情况的节点，只保留原始链表中 **没有重复出现** 的数字。

返回同样按升序排列的结果链表。

**示例1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/linkedlist1.jpg)

```
输入：head = [1,2,3,3,4,4,5]
输出：[1,2,5]
```

**示例 2：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/linkedlist2.jpg)

```
输入：head = [1,1,1,2,3]
输出：[2,3]
```

**提示：**

- 链表中节点数目在范围 `[0, 300]` 内
- `-100 <= Node.val <= 100`
- 题目数据保证链表已经按升序排列

### 题目解法

```
package algorithm;

public class RemoveDuplicatesFromSortedListII {

    public static ListNode deleteDuplicates(ListNode head) {
        if (head == null) {
            return head;
        }

        ListNode dummy = new ListNode(0, head);

        ListNode cur = dummy;
        while (cur.next != null && cur.next.next != null) {
            if (cur.next.val == cur.next.next.val) {
                int x = cur.next.val;
                while (cur.next != null && cur.next.val == x) {
                    cur.next = cur.next.next;
                }
            } else {
                cur = cur.next;
            }
        }

        return dummy.next;
    }

    public static void main(String[] args) {
        // 1,2,3,3,4,4,5
        ListNode n1 = new ListNode(1);
        ListNode n2 = new ListNode(2);
        n1.next = n2;
        ListNode n3 = new ListNode(3);
        n2.next = n3;
        ListNode n4 = new ListNode(3);
        n3.next = n4;
        ListNode n5 = new ListNode(4);
        n4.next = n5;
        ListNode n6 = new ListNode(4);
        n5.next = n6;
        ListNode n7 = new ListNode(5);
        n6.next = n7;
        print(deleteDuplicates(n1));

        // 1,1,1,2,3
        n1 = new ListNode(1);
        n2 = new ListNode(1);
        n1.next = n2;
        n3 = new ListNode(1);
        n2.next = n3;
        n4 = new ListNode(2);
        n3.next = n4;
        n5 = new ListNode(3);
        n4.next = n5;
        print(deleteDuplicates(n1));

        n1 = new ListNode(1);
        n2 = new ListNode(1);
        n1.next = n2;
        print(deleteDuplicates(n1));
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
            System.out.print(head.val + " ");
            head = head.next;
        }
        System.out.println();
    }
}
```

打印：

```
1 2 5 
2 3 
```

**思路：**

思路很简单。就是加一个哑节点。注意，不要思考过于复杂。