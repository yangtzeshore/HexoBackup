---
title: MinimumDepthOfBinaryTree
date: 2021-07-25 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 二叉树的最小深度

- [题目介绍](https://yangtzeshore.github.io/2021/07/25/MinimumDepthOfBinaryTree/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/07/25/MinimumDepthOfBinaryTree/#题目解法)

### 题目介绍

#### [二叉树的最小深度](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/)

给定一个二叉树，找出其最小深度。

最小深度是从根节点到最近叶子节点的最短路径上的节点数量。

**说明：**叶子节点是指没有子节点的节点。

**示例 1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/balance_1.jpg)

```
输入：root = [3,9,20,null,null,15,7]
输出：2
```

**示例 2：**

```
输入：root = [2,null,3,null,4,null,5,null,6]
输出：5
```

**提示：**

- 树中节点数的范围在 [0,105] 内
- `-1000 <= Node.val <= 1000`

### 题目解法

```
package algorithm;

public class MinimumDepthOfBinaryTree {

    public int minDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }

        if (root.left == null && root.right == null) {
            return 1;
        }

        int min_depth = Integer.MAX_VALUE;
        if (root.left != null) {
            min_depth = Math.min(minDepth(root.left), min_depth);
        }
        if (root.right != null) {
            min_depth = Math.min(minDepth(root.right), min_depth);
        }

        return min_depth + 1;
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

思路上，递归调用分解问题。对于递归的理解需要加强。