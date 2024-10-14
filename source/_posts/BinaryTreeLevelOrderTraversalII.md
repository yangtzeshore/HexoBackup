---
title: BinaryTreeLevelOrderTraversalII
date: 2021-07-16 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 二叉树的层序遍历 II

- [题目介绍](https://yangtzeshore.github.io/2021/07/16/BinaryTreeLevelOrderTraversalII/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/07/16/BinaryTreeLevelOrderTraversalII/#题目解法)

### 题目介绍

#### [二叉树的层序遍历 II](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)

给定一个二叉树，返回其节点值自底向上的层序遍历。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）

例如：
给定二叉树 `[3,9,20,null,null,15,7]`,

```
  3
 / \
9  20
  /  \
 15   7
```

返回其自底向上的层序遍历为：

```
[
  [15,7],
  [9,20],
  [3]
]
```

### 题目解法

```
package algorithm;

import java.util.LinkedList;
import java.util.List;
import java.util.Queue;

public class BinaryTreeLevelOrderTraversalII {

    public static List<List<Integer>> levelOrderBottom(TreeNode root) {

        List<List<Integer>> ans = new LinkedList<>();
        if (root == null) {
            return ans;
        }
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            List<Integer> temp = new LinkedList<>();
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode node = queue.poll();
                temp.add(node.val);

                if (node.left != null) {
                    queue.offer(node.left);
                }
                if (node.right != null) {
                    queue.offer(node.right);
                }
            }
            ans.add(0, new LinkedList<>(temp));
        }

        return ans;
    }

    public static void main(String[] args) {
        TreeNode n1 = new TreeNode(3);
        TreeNode n2 = new TreeNode(9);
        n1.left = n2;
        TreeNode n3 = new TreeNode(20);
        n1.right = n3;
        TreeNode n4 = new TreeNode(15);
        n2.left = null;
        n2.right = null;
        n3.left = n4;
        TreeNode n5 = new TreeNode(7);
        n3.right = n5;

        levelOrderBottom(n1);
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

思路上，其实和z字形很类似。只不过借助于java的api实现头插入。以后可以试试Go或者C的写法。