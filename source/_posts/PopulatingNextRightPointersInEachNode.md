---
title: PopulatingNextRightPointersInEachNode
date: 2021-08-04 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 填充每个节点的下一个右侧节点指针

- [题目介绍](https://yangtzeshore.github.io/2021/08/04/PopulatingNextRightPointersInEachNode/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/08/04/PopulatingNextRightPointersInEachNode/#题目解法)

### 题目介绍

#### [填充每个节点的下一个右侧节点指针](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)

给定一个 **完美二叉树** ，其所有叶子节点都在同一层，每个父节点都有两个子节点。二叉树定义如下：

```
struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
```

填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 `NULL`。

初始状态下，所有 next 指针都被设置为 `NULL`。

**进阶：**

- 你只能使用常量级额外空间。
- 使用递归解题也符合要求，本题中递归程序占用的栈空间不算做额外的空间复杂度。

**示例 ：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/116_sample.png)

```
输入：root = [1,2,3,4,5,6,7]
输出：[1,#,2,3,#,4,5,6,7,#]
解释：给定二叉树如图 A 所示，你的函数应该填充它的每个 next 指针，以指向其下一个右侧节点，如图 B 所示。序列化的输出按层序遍历排列，同一层节点由 next 指针连接，'#' 标志着每一层的结束。
```

**提示：**

- 树中节点的数量少于 `4096`
- `-1000 <= node.val <= 1000`

### 题目解法

```
package algorithm;

public class PopulatingNextRightPointersInEachNode {

    public Node connect(Node root) {
        if (root == null) {
            return root;
        }

        // 从根节点开
        Node leftmost = root;

        while (leftmost.left != null) {

            // 遍历这一层节点组织成的链表，为下一层的节点更新 next 指针
            Node head = leftmost;

            while (head != null) {

                // CONNECTION 1
                head.left.next = head.right;

                // CONNECTION 2
                if (head.next != null) {
                    head.right.next = head.next.left;
                }

                // 指针向后移动
                head = head.next;
            }

            // 去下一层的最左的节点
            leftmost = leftmost.left;
        }

        return root;
    }

    class Node {
        public int val;
        public Node left;
        public Node right;
        public Node next;

        public Node() {}

        public Node(int _val) {
            val = _val;
        }

        public Node(int _val, Node _left, Node _right, Node _next) {
            val = _val;
            left = _left;
            right = _right;
            next = _next;
        }
    }
}
```

打印：

```

```

**思路：**

思路上，利用完美二叉树特点和上层已经链接的线索，这道题这么简单，看了答案，看来编程需要努力。