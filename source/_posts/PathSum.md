---
title: PathSum
date: 2021-07-26 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 路径总和

- [题目介绍](https://yangtzeshore.github.io/2021/07/26/PathSum/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/07/26/PathSum/#题目解法)

### 题目介绍

#### [路径总和](https://leetcode-cn.com/problems/path-sum/)

给你二叉树的根节点 `root` 和一个表示目标和的整数 `targetSum` ，判断该树中是否存在 **根节点到叶子节点** 的路径，这条路径上所有节点值相加等于目标和 `targetSum` 。

**叶子节点** 是指没有子节点的节点。

**示例 1：**

![img](https://assets.leetcode.com/uploads/2021/01/18/pathsum1.jpg)

```
输入：root = [5,4,8,11,null,13,4,7,2,null,null,null,1], targetSum = 22
输出：true
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2021/01/18/pathsum2.jpg)

```
输入：root = [1,2,3], targetSum = 5
输出：false
```

**示例 3：**

```
输入：root = [1,2], targetSum = 0
输出：false
```

**提示：**

- 树中节点的数目在范围 `[0, 5000]` 内
- `-1000 <= Node.val <= 1000`
- `-1000 <= targetSum <= 1000`

### 题目解法

```
package algorithm;

public class PathSum {

    private boolean flag = false;
    public boolean hasPathSum(TreeNode root, int targetSum) {
        if (root == null) {
            return false;
        }
        dfs(root, targetSum, root.val);
        return flag;
    }

    private void dfs(TreeNode root, int targetSum, int sumTemp) {
        if (root.left == null && root.right == null) {
            if (sumTemp == targetSum) {
                flag = true;
            }
        }
        if (root.left != null) {
            dfs(root.left, targetSum, sumTemp + root.left.val);
        }
        if (root.right != null) {
            dfs(root.right, targetSum, sumTemp + root.right.val);
        }
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