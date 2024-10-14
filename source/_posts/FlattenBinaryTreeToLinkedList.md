---
title: FlattenBinaryTreeToLinkedList
date: 2021-07-31 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 二叉树展开为链表

- [题目介绍](https://yangtzeshore.github.io/2021/07/31/FlattenBinaryTreeToLinkedList/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/07/31/FlattenBinaryTreeToLinkedList/#题目解法)

### 题目介绍

#### [二叉树展开为链表](https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/)

给你二叉树的根结点 `root` ，请你将它展开为一个单链表：

- 展开后的单链表应该同样使用 `TreeNode` ，其中 `right` 子指针指向链表中下一个结点，而左子指针始终为 `null` 。
- 展开后的单链表应该与二叉树 [**先序遍历**](https://baike.baidu.com/item/先序遍历/6442839?fr=aladdin) 顺序相同。

**示例 1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/flaten.jpg)

```
输入：root = [1,2,5,3,4,null,6]
输出：[1,null,2,null,3,null,4,null,5,null,6]
```

**示例 2：**

```
输入：root = []
输出：[]
```

**示例 3：**

```
输入：root = [0]
输出：[0]
```

**提示：**

- 树中结点数在范围 `[0, 2000]` 内
- `-100 <= Node.val <= 100`

### 题目解法

```
package algorithm;

import java.util.LinkedList;
import java.util.Queue;

public class FlattenBinaryTreeToLinkedList {

    static Queue<TreeNode> queue = new LinkedList<>();

    public static void flatten(TreeNode root) {
        queue.clear();
        dfs(root);
        TreeNode lamb = new TreeNode();
        while (queue.size() >= 1) {
            TreeNode node = queue.poll();
            node.left = null;
            node.right = null;
            lamb.right = node;
            lamb = lamb.right;
        }
        System.out.println();
    }

    private static void dfs(TreeNode root) {
        if (root == null) {
            return;
        }
        queue.offer(root);
        dfs(root.left);
        dfs(root.right);
    }

    public static void main(String[] args) {

        // 1,2,3,3,4,4,5
        TreeNode n1 = new TreeNode(1);
        TreeNode n2 = new TreeNode(2);
        n1.left = n2;
        TreeNode n3 = new TreeNode(5);
        n1.right = n3;
        TreeNode n4 = new TreeNode(3);
        n2.left = n4;
        TreeNode n5 = new TreeNode(4);
        n2.right = n5;
        TreeNode n6 = new TreeNode(6);
        n3.right = n6;

        flatten(n1);
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
}
```

打印：

```

```

**思路：**

思路上，很简单。效率当然也是一般。后续有时间可以优化一下思路。