---
title: SymmetricTree
date: 2021-07-09 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 对称二叉树

- [题目介绍](https://yangtzeshore.github.io/2021/07/09/SymmetricTree/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/07/09/SymmetricTree/#题目解法)

### 题目介绍

#### [对称二叉树](https://leetcode-cn.com/problems/symmetric-tree/)

给定一个二叉树，检查它是否是镜像对称的。

例如，二叉树 `[1,2,2,3,4,4,3]` 是对称的。

**示例 1：**

```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```

但是下面这个 `[1,2,2,null,3,null,3]` 则不是镜像对称的:

```
  1
 / \
2   2
 \   \
 3    3
```

**进阶：**

你可以运用递归和迭代两种方法解决这个问题吗？

### 题目解法

```
package algorithm;

import java.util.LinkedList;
import java.util.Queue;

public class SymmetricTree {

    public static boolean isSymmetric(TreeNode root) {
        return dfs(root.left, root.right);
    }

    private static boolean dfs(TreeNode p, TreeNode q) {
        if (p == null && q == null) {
            return true;
        } else if (p != null && q != null) {
            if (dfs(p.left, q.right) && p.val == q.val) {
                return dfs(p.right, q.left);
            }
        }
        return false;
    }

    public boolean isSymmetric1(TreeNode root) {
        return check(root, root);
    }

    public boolean check(TreeNode u, TreeNode v) {
        Queue<TreeNode> q = new LinkedList<>();
        q.offer(u);
        q.offer(v);
        while (!q.isEmpty()) {
            u = q.poll();
            v = q.poll();
            if (u == null && v == null) {
                continue;
            }
            if ((u == null || v == null) || (u.val != v.val)) {
                return false;
            }

            q.offer(u.left);
            q.offer(v.right);

            q.offer(u.right);
            q.offer(v.left);
        }
        return true;
    }

    public static void main(String[] args) {

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

思路上，很简单了，就是递归比对。如果是迭代，需要栈来承载比对的数据。