---
title: BalancedBinaryTree
date: 2021-07-19 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 平衡二叉树

- [题目介绍](https://yangtzeshore.github.io/2021/07/19/BalancedBinaryTree/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/07/19/BalancedBinaryTree/#题目解法)

### 题目介绍

#### [平衡二叉树](https://leetcode-cn.com/problems/balanced-binary-tree/)

给定一个二叉树，判断它是否是高度平衡的二叉树。

本题中，一棵高度平衡二叉树定义为：

> 一个二叉树*每个节点* 的左右两个子树的高度差的绝对值不超过 1 。

**示例 1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/balance_1.jpg)

```
输入：root = [3,9,20,null,null,15,7]
输出：true
```

**示例 2：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/balance_2.jpg)

```
输入：root = [1,2,2,3,3,null,null,4,4]
输出：false
```

**示例 3：**

```
输入：root = []
输出：true
```

**提示：**

- 树中的节点数在范围 `[0, 5000]` 内
- −104<=Node.val<=104

### 题目解法

```
package algorithm;

public class BalancedBinaryTree {

    public boolean isBalanced(TreeNode root) {
        return dfs(root);
    }

    private boolean dfs(TreeNode root) {
        if (root == null) {
            return true;
        }

        int left = deep(root.left);
        int right = deep(root.right);
        if (Math.abs(left - right) > 1) {
            return false;
        }
        if (!dfs(root.left)) {
            return false;
        }
        if (!dfs(root.right)) {
            return false;
        }
        return true;
    }

    private int deep(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int left = deep(root.left) + 1;
        int right = deep(root.right) + 1;
        return Math.max(left, right);
    }

    public static void main(String[] args) {

    }

    public class TreeNode {
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

思路上，两次递归。先遍历，然后找出相应节点的深度。