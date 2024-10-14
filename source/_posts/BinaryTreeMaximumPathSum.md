---
title: BinaryTreeMaximumPathSum
date: 2021-08-31 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 二叉树中的最大路径和

- [题目介绍](https://yangtzeshore.github.io/2021/08/31/BinaryTreeMaximumPathSum/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/08/31/BinaryTreeMaximumPathSum/#题目解法)

### 题目介绍

#### [二叉树中的最大路径和](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)

**路径** 被定义为一条从树中任意节点出发，沿父节点-子节点连接，达到任意节点的序列。同一个节点在一条路径序列中 **至多出现一次** 。该路径 **至少包含一个** 节点，且不一定经过根节点。

**路径和** 是路径中各节点值的总和。

给你一个二叉树的根节点 `root` ，返回其 **最大路径和** 。

**示例 1:**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/exx1.jpg)

```
输入：root = [1,2,3]
输出：6
解释：最优路径是 2 -> 1 -> 3 ，路径和为 2 + 1 + 3 = 6
```

**示例 2:**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/exx2.jpg)

```
输入：root = [-10,9,20,null,null,15,7]
输出：42
解释：最优路径是 15 -> 20 -> 7 ，路径和为 15 + 20 + 7 = 42
```

**提示：**

- 树中节点数目范围是 [1,3∗104]
- `-1000 <= Node.val <= 1000`

### 题目解法

```
int max = Integer.MIN_VALUE;
   public int maxPathSum(TreeNode root) {
       maxGain(root);
       return max;
   }

   private int maxGain (TreeNode root) {
       int gain;
       int maxPath;
       if (root == null) {
           return  0;
       } else if (root.left == null && root.right == null) {
           gain = maxPath = root.val;
       } else {
           int left = Math.max(0, maxGain(root.left));
           int right = Math.max(0, maxGain(root.right));
           maxPath = root.val + left +right;
           gain = root.val + Math.max(left, right);
       }
       if (maxPath > max) {
           max = maxPath;
       }
       return gain;
   }
```

打印：

```

```

**思路：**

思路上，需要想通父节点的作用，一个是给上面节点贡献值，一个是自己也有可能是某条路径最大值。