---
title: SameTree
date: 2021-07-08 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 相同的树

- [题目介绍](https://yangtzeshore.github.io/2021/07/08/SameTree/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/07/08/SameTree/#题目解法)

### 题目介绍

#### [相同的树](https://leetcode-cn.com/problems/same-tree/)

给你两棵二叉树的根节点 `p` 和 `q` ，编写一个函数来检验这两棵树是否相同。

如果两个树在结构上相同，并且节点具有相同的值，则认为它们是相同的。

**示例 1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/ex1.jpg)

```
输入：p = [1,2,3], q = [1,2,3]
输出：true
```

**示例 2：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/ex2.jpg)

```
输入：p = [1,2], q = [1,null,2]
输出：false
```

**示例 3：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/ex3.jpg)

```
输入：p = [1,2,1], q = [1,1,2]
输出：false
```

**提示：**

- 两棵树上的节点数目都在范围 `[0, 100]` 内
- −104<=Node.val<=104

### 题目解法

```
package algorithm;

public class SameTree {

    public static boolean isSameTree(TreeNode p, TreeNode q) {
        return dfs(p, q);
    }

    private static boolean dfs(TreeNode p, TreeNode q) {
        if (p == null && q == null) {
            return true;
        } else if (p != null && q != null && p.val == q.val) {
            if (dfs(p.left, q.left)) {
                return dfs(p.right, q.right);
            }
        }
        return false;
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

思路上，中序遍历，然后挨个判断，非常简单。