---
title: ValidateBinarySearchTree
date: 2021-07-06 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 验证二叉搜索树

- [题目介绍](https://yangtzeshore.github.io/2021/07/04/ValidateBinarySearchTree/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/07/04/ValidateBinarySearchTree/#题目解法)

### 题目介绍

#### [验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)

给定一个二叉树，判断其是否是一个有效的二叉搜索树。

假设一个二叉搜索树具有如下特征：

- 节点的左子树只包含**小于**当前节点的数。
- 节点的右子树只包含**大于**当前节点的数。
- 所有左子树和右子树自身必须也是二叉搜索树。

**示例 1：**

```
输入:
    2
   / \
  1   3
输出: true
```

**示例 2：**

```
输入:
    5
   / \
  1   4
     / \
    3   6
输出: false
解释: 输入为: [5,1,4,null,null,3,6]。
     根节点的值为 5 ，但是其右子节点值为 4 。
```

### 题目解法

```
package algorithm;

public class ValidateBinarySearchTree {

    public static boolean isValidBST(TreeNode root) {
        return isValidBST(root, Long.MIN_VALUE, Long.MAX_VALUE);
    }

    public static boolean isValidBST(TreeNode node, long lower, long upper) {
        if (node == null) {
            return true;
        }
        if (node.val <= lower || node.val >= upper) {
            return false;
        }
        return isValidBST(node.left, lower, node.val) && isValidBST(node.right, node.val, upper);
    }

    public static void main(String[] args) {
        TreeNode n1 = new TreeNode(2);
        TreeNode n2 = new TreeNode(1);
        n1.left = n2;
        TreeNode n3 = new TreeNode(3);
        n1.right = n3;

        System.out.println(isValidBST(n1));

        n1 = new TreeNode(5);
        n2 = new TreeNode(1);
        n1.left = n2;
        n3 = new TreeNode(4);
        n1.right = n3;
        TreeNode n4 = new TreeNode(3);
        n3.left = n4;
        TreeNode n5 = new TreeNode(6);
        n3.right = n5;
        System.out.println(isValidBST(n1));
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
true
false
```

**思路：**

思路上，递归判断range。中序遍历是第一个想到的，可能比较抽象了。