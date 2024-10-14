---
title: ConvertSortedArrayToBinarySearchTree
date: 2021-07-17 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 将有序数组转换为二叉搜索树

- [题目介绍](https://yangtzeshore.github.io/2021/07/17/ConvertSortedArrayToBinarySearchTree/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/07/17/ConvertSortedArrayToBinarySearchTree/#题目解法)

### 题目介绍

#### [将有序数组转换为二叉搜索树](https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/)

给你一个整数数组 `nums` ，其中元素已经按 **升序** 排列，请你将其转换为一棵 **高度平衡** 二叉搜索树。

**高度平衡** 二叉树是一棵满足「每个节点的左右两个子树的高度差的绝对值不超过 1 」的二叉树。

**示例 1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/btree1.jpg)

```
输入：nums = [-10,-3,0,5,9]
输出：[0,-3,9,-10,null,5]
解释：[0,-10,5,null,-3,null,9] 也将被视为正确答案：
```

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/btree2.jpg)

**示例 2：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/btree.jpg)

```
输入：nums = [1,3]
输出：[3,1]
解释：[1,3] 和 [3,1] 都是高度平衡二叉搜索树。
```

**提示：**

- 1<=nums.length<=104
- −104<=nums[i]<=104
- `nums` 按 **严格递增** 顺序排列

### 题目解法

```
package algorithm;

public class ConvertSortedArrayToBinarySearchTree {

    public TreeNode sortedArrayToBST(int[] nums) {

        int n = nums.length;
        return dfs(nums, 0, n - 1);
    }

    private TreeNode dfs(int[] nums, int left, int right) {
        if (left > right) {
            return null;
        }
        int mid = (left +right) / 2;
        int root = nums[mid];
        TreeNode node = new TreeNode(root);
        node.left = dfs(nums, left, mid - 1);
        node.right = dfs(nums, mid +1, right);
        return node;
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

思路上，其实借鉴了之前的算法题。也很简单，如果是用递归的话。