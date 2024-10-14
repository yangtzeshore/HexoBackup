---
title: BinaryTreeZigzagLevelOrderTraversal
date: 2021-07-11 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 二叉树的锯齿形层序遍历

- [题目介绍](https://yangtzeshore.github.io/2021/07/11/BinaryTreeZigzagLevelOrderTraversal/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/07/11/BinaryTreeZigzagLevelOrderTraversal/#题目解法)

### 题目介绍

#### [二叉树的锯齿形层序遍历](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

给定一个二叉树，返回其节点值的锯齿形层序遍历。（即先从左往右，再从右往左进行下一层遍历，以此类推，层与层之间交替进行）。

例如：
给定二叉树 `[3,9,20,null,null,15,7]`,

```
  3
 / \
9  20
  /  \
 15   7
```

返回锯齿形层序遍历如下：

```
[
  [3],
  [20,9],
  [15,7]
]
```

### 题目解法

```
package algorithm;

import java.util.*;

public class BinaryTreeZigzagLevelOrderTraversal {

    public static List<List<Integer>> zigzagLevelOrder(TreeNode root) {

        List<List<Integer>> ans = new LinkedList<>();
        if (root == null) {
            return ans;
        }

        Queue<TreeNode> nodeQueue = new LinkedList<>();
        nodeQueue.offer(root);
        boolean isOrderLeft = true;

        while (!nodeQueue.isEmpty()) {
            Deque<Integer> levelList = new LinkedList<>();
            int size = nodeQueue.size();
            for (int i = 0; i < size; ++i) {
                // 从头取
                TreeNode curNode = nodeQueue.poll();
                if (isOrderLeft) {
                    levelList.offerLast(curNode.val);
                } else {
                    levelList.offerFirst(curNode.val);
                }
                if (curNode.left != null) {
                    nodeQueue.offer(curNode.left);
                }
                if (curNode.right != null) {
                    nodeQueue.offer(curNode.right);
                }
            }
            ans.add(new LinkedList<>(levelList));
            isOrderLeft = !isOrderLeft;
        }

        return ans;
    }


    public static void main(String[] args) {

        TreeNode n1 = new TreeNode(1);
        TreeNode n2 = new TreeNode(2);
        n1.left = n2;
        TreeNode n3 = new TreeNode(3);
        n1.right = n3;
        TreeNode n4 = new TreeNode(4);
        n2.left = n4;
        n2.right = null;
        TreeNode n5 = new TreeNode(5);
        n3.right = n5;
        n3.left = null;

        List<List<Integer>> lists = zigzagLevelOrder(n1);
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
1 
3 2 
4 5 
```

**思路：**

思路上，利用了java的linkedqueue的队列特性，而且是两个队列。