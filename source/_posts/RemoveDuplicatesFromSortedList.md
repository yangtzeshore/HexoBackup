---
title: RemoveDuplicatesFromSortedList
date: 2021-06-03 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 删除排序链表中的重复元素

- [题目介绍](https://yangtzeshore.github.io/2021/06/03/RemoveDuplicatesFromSortedList/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/06/03/RemoveDuplicatesFromSortedList/#题目解法)

### 题目介绍

#### [删除排序链表中的重复元素](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

存在一个按升序排列的链表，给你这个链表的头节点 `head` ，请你删除所有重复的元素，使每个元素 **只出现一次** 。

返回同样按升序排列的结果链表。

**示例1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/list1.jpg)

```
输入：head = [1,1,2]
输出：[1,2]
```

**示例 2：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/list2.jpg)

```
输入：head = [1,1,2,3,3]
输出：[1,2,3]
```

**提示：**

- 链表中节点数目在范围 `[0, 300]` 内
- `-100 <= Node.val <= 100`
- 题目数据保证链表已经按升序排列

### 题目解法

```
package algorithm;

public class RemoveDuplicatesFromSortedList {

    public static ListNode deleteDuplicates(ListNode head) {

        if (head == null) {
            return head;
        }

        ListNode cur = head;
        while (cur.next != null) {
            if (cur.val == cur.next.val) {
                cur.next = cur.next.next;
            } else {
                cur = cur.next;
            }
        }
        return head;
    }

    public static void main(String[] args) {

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

思路很简单。就是遍历并修改指向的重复节点。