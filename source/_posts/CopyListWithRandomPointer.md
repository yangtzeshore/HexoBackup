---
title: CopyListWithRandomPointer
date: 2022-01-05 09:22:21
tags:
- 
categories:
- Leetcode 
---



# CopyListWithRandomPointer

# [复制带随机指针的链表](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)

- [题目介绍](https://yangtzeshore.github.io/2022/01/05/CopyListWithRandomPointer/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2022/01/05/CopyListWithRandomPointer/#题目解法)

### 题目介绍

#### 复制带随机指针的链表

给你一个长度为 `n` 的链表，每个节点包含一个额外增加的随机指针 `random` ，该指针可以指向链表中的任何节点或空节点。

构造这个链表的 **[深拷贝](https://baike.baidu.com/item/深拷贝/22785317?fr=aladdin)**。 深拷贝应该正好由 `n` 个 **全新** 节点组成，其中每个新节点的值都设为其对应的原节点的值。新节点的 `next` 指针和 `random` 指针也都应指向复制链表中的新节点，并使原链表和复制链表中的这些指针能够表示相同的链表状态。**复制链表中的指针都不应指向原链表中的节点** 。

例如，如果原链表中有 `X` 和 `Y` 两个节点，其中 `X.random --> Y` 。那么在复制链表中对应的两个节点 `x` 和 `y` ，同样有 `x.random --> y` 。

返回复制链表的头节点。

用一个由 `n` 个节点组成的链表来表示输入/输出中的链表。每个节点用一个 `[val, random_index]` 表示：

- `val`：一个表示 `Node.val` 的整数。
- `random_index`：随机指针指向的节点索引（范围从 `0` 到 `n-1`）；如果不指向任何节点，则为 `null` 。

你的代码 **只** 接受原链表的头节点 `head` 作为传入参数。

**示例 1:**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/e1.png)

```
输入：head = [[7,null],[13,0],[11,4],[10,2],[1,0]]
输出：[[7,null],[13,0],[11,4],[10,2],[1,0]]
```

**示例 2:**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/e2.png)

```
输入：head = [[1,1],[2,1]]
输出：[[1,1],[2,1]]
```

**示例 3:**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/e3.png)

```
输入：head = [[3,null],[3,0],[3,null]]
输出：[[3,null],[3,0],[3,null]]
```

**示例 4:**

```
输入：head = []
输出：[]
解释：给定的链表为空（空指针），因此返回 null。
```

**提示：**

- `0 <= n <= 1000`
- `-10000 <= Node.val <= 10000`
- `Node.random` 为空（null）或指向链表中的节点。

#### 题目解法

```
Map<Node, Node> map2 = new HashMap<>();
    public Node copyRandomList(Node head) {

        if (head == null) {
            return null;
        }
        map2.clear();
        Node dumb = new Node(0);
        Node cur = head;
        Node prev = dumb;
        int count = 0;
        while (cur != null) {
            Node newNode = new Node(cur.val);
            map2.put(cur, newNode);
            count++;
            prev.next = newNode;
            prev = newNode;
            cur = cur.next;
        }

        cur = head;
        prev = dumb;
        count = 0;
        while (cur != null) {
            if (cur.random != null) {
                prev.next.random = map2.get(cur.random);
            }
            count++;
            prev = prev.next;
            cur = cur.next;
        }
        return dumb.next;
    }
```

打印：

```

```

**思路：**

思路上，不难，就是需要记录random的映射关系。