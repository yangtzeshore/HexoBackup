---
title: MergeKSortedLists
date: 2021-01-26 09:22:21
tags:
- 
categories:
- Leetcode 
---



## 合并K个升序链表

- [题目介绍](https://yangtzeshore.github.io/2021/01/26/MergeKSortedLists/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/01/26/MergeKSortedLists/#题目解法)

### 题目介绍

[合并K个升序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

**示例 1：**

```
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
解释：链表数组如下：
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6
```

**示例 2：**

```
输入：lists = []
输出：[]
```

**示例 3：**

```
输入：lists = [[]]
输出：[]
```

**提示：**

- `k == lists.length`
- `0 <= k <= 10^4`
- `0 <= lists[i].length <= 500`
- `-10^4 <= lists[i][j] <= 10^4`
- `lists[i]` 按 **升序** 排列
- `lists[i].length` 的总和不超过 `10^4`

这里：元素为[]在java就是null，列表为空就是数组大小为空。

### 题目解法

```
package algorithm;

public class MergeKSortedLists {

    public static ListNode mergeKLists(ListNode[] lists) {

        if (lists.length == 0) {
            return null;
        }

        if (lists.length == 1) {
            return lists[0];
        }

        ListNode head = new ListNode(Integer.MAX_VALUE);
        ListNode cur = head;
        ListNode prev = cur;
        int index = -1;
        while (true) {
            int count = 0;
            for (int i = 0; i < lists.length; i++) {
                if (lists[i] == null) {
                    count ++;
                    if (count == lists.length && index > -1) {
                        prev.next = null;
                        return head;
                    } else if (count == lists.length) {
                        return null;
                    }
                    continue;
                }

                if (lists[i] != null && lists[i].val <= cur.val) {
                    cur.val = lists[i].val;
                    index = i;
                }
            }
            if (lists[index] != null) {
                lists[index] = lists[index].next;
            }

            cur.next = new ListNode(Integer.MAX_VALUE);
            prev = cur;
            cur = cur.next;
        }
    }

    public static void main(String[] args) {

        // lists = [[1,4,5],[1,3,4],[2,6]]
        ListNode a1 = new ListNode(1);
        ListNode a2 = new ListNode(4);
        a1.next = a2;
        ListNode a3 = new ListNode(5);
        a2.next = a3;

        ListNode a4 = new ListNode(1);
        ListNode a5 = new ListNode(3);
        a4.next = a5;
        ListNode a6 = new ListNode(4);
        a5.next = a6;

        ListNode a7 = new ListNode(2);
        ListNode a8 = new ListNode(6);
        a7.next = a8;

        ListNode[] lists = new ListNode[]{a1, a4, a7};
        print(mergeKLists(lists));
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
11234456
```

**思路：**

这道题依然想出了三种思路，大致对应了官方的三种思路。第一种：一个一个合并，直到结束；第二种思路，就是两两归并，没想到效率这么高，所以计算复杂度是需要考虑到的；第三种，也是自己给出的结果，效率不是很理想，参考了官方答案，没想到用了PriorityQueue，利用优先队列本身的排序机制（小顶堆），非常巧妙。