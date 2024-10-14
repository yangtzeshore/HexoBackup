---
title: BinaryTreeLevelOrderTraversal
date: 2021-07-10 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 二叉树的层序遍历

- [题目介绍](https://yangtzeshore.github.io/2021/07/10/BinaryTreeLevelOrderTraversal/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/07/10/BinaryTreeLevelOrderTraversal/#题目解法)

### 题目介绍

#### [二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

给你一个二叉树，请你返回其按 **层序遍历** 得到的节点值。 （即逐层地，从左到右访问所有节点）。

**示例：**

二叉树：`[3,9,20,null,null,15,7]`,

```
  3
 / \
9  20
  /  \
 15   7
```

返回其层序遍历结果：

```
[
  [3],
  [9,20],
  [15,7]
]
```

### 题目解法

```
package algorithm;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class BinaryTreeLevelOrderTraversal {

    static Map<Integer, List<Integer>> map = new HashMap<>();

    public static List<List<Integer>> levelOrder(TreeNode root) {

        union(root, 0);
        List<List<Integer>> ans = new ArrayList<>();
        for (Map.Entry<Integer, List<Integer>> entry : map.entrySet()) {
            List<Integer> value = entry.getValue();
            ans.add(value);
        }
        return ans;
    }

    private static void union(TreeNode root, int count) {
        if (root == null) {
            return;
        }
        if (map.get(count) == null) {
            List<Integer> list = new ArrayList<>();
            list.add(root.val);
            map.put(count, list);
        } else {
            List<Integer> list = map.get(count);
            list.add(root.val);
        }
        union(root.left, count + 1);
        union(root.right, count + 1);
    }

    public static void main(String[] args) {
        TreeNode n1 = new TreeNode(3);
        TreeNode n2 = new TreeNode(9);
        n1.left = n2;
        TreeNode n3 = new TreeNode(20);
        n1.right = n3;
        TreeNode n4 = new TreeNode(15);
        n3.left =n4;
        TreeNode n5 = new TreeNode(7);
        n3.right = n5;

        List<List<Integer>> lists = levelOrder(n1);
        for (List<Integer> list : lists) {
            for (Integer integer : list) {
                System.out.print(integer + " ");
            }
            System.out.println();
        }

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
3 
9 20 
15 7 
```

**思路：**

思路上，很简单了，就是增加深度这个维度，最后统计出来。