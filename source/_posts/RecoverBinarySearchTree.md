---
title: RecoverBinarySearchTree
date: 2021-07-06 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 恢复二叉搜索树

- [题目介绍](https://yangtzeshore.github.io/2021/07/06/RecoverBinarySearchTree/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/07/06/RecoverBinarySearchTree/#题目解法)

### 题目介绍

#### [恢复二叉搜索树](https://leetcode-cn.com/problems/recover-binary-search-tree/)

给你二叉搜索树的根节点 `root` ，该树中的两个节点被错误地交换。请在不改变其结构的情况下，恢复这棵树。

**进阶：**使用 O(*n*) 空间复杂度的解法很容易实现。你能想出一个只使用常数空间的解决方案吗？

**示例 1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/recover1.jpg)

```
输入：root = [1,3,null,null,2]
输出：[3,1,null,null,2]
解释：3 不能是 1 左孩子，因为 3 > 1 。交换 1 和 3 使二叉搜索树有效。
```

**示例 2：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/recover2.jpg)

```
输入：root = [3,1,4,null,null,2]
输出：[2,1,4,null,null,3]
解释：2 不能在 3 的右子树中，因为 2 < 3 。交换 2 和 3 使二叉搜索树有效。
```

### 题目解法

```
package algorithm;

public class RecoverBinarySearchTree {

    public void recoverTree(TreeNode root) {

        TreeNode x = null, y = null, pred = null, predecessor;

        while (root != null) {
            if (root.left != null) {
                // predecessor 节点就是当前 root 节点向左走一步，然后一直向右走至无法走为止
                predecessor = root.left;
                while (predecessor.right != null && predecessor.right != root) {
                    predecessor = predecessor.right;
                }

                // 让 predecessor 的右指针指向 root，继续遍历左子树
                if (predecessor.right == null) {
                    predecessor.right = root;
                    root = root.left;
                }
                // 说明左子树已经访问完了，我们需要断开链接
                else {
                    if (pred != null && root.val < pred.val) {
                        y = root;
                        if (x == null) {
                            x = pred;
                        }
                    }
                    pred = root;

                    predecessor.right = null;
                    root = root.right;
                }
            }
            // 如果没有左孩子，则直接访问右孩子
            else {
                if (pred != null && root.val < pred.val) {
                    y = root;
                    if (x == null) {
                        x = pred;
                    }
                }
                pred = root;
                root = root.right;
            }
        }
        swap(x, y);
    }

    public void swap(TreeNode x, TreeNode y) {
        int tmp = x.val;
        x.val = y.val;
        y.val = tmp;
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

思路上，可定都会想到中序遍历了，无论是递归还是非递归都是不会常数空间复杂度的。morris遍历，是进阶题解的方法。目前对于Morris算法不是和熟悉，需要多联系。