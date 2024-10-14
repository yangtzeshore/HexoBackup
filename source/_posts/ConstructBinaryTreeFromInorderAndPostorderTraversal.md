---
title: ConstructBinaryTreeFromInorderAndPostorderTraversal
date: 2021-07-15 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 从中序与后序遍历序列构造二叉树

- [题目介绍](https://yangtzeshore.github.io/2021/07/15/ConstructBinaryTreeFromInorderAndPostorderTraversal/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/07/15/ConstructBinaryTreeFromInorderAndPostorderTraversal/#题目解法)

### 题目介绍

#### [从中序与后序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

根据一棵树的中序遍历与后序遍历构造二叉树。

**注意:**
你可以假设树中没有重复的元素。

例如，给出

```
中序遍历 inorder = [9,3,15,20,7]
后序遍历 postorder = [9,15,7,20,3]
```

返回如下的二叉树：

```
  3
 / \
9  20
  /  \
 15   7
```

### 题目解法

```
package algorithm;

import java.util.HashMap;
import java.util.Map;

public class ConstructBinaryTreeFromInorderAndPostorderTraversal {

    private Map<Integer, Integer> map;

    public TreeNode buildTree(int[] inorder, int[] postorder) {

        int n = inorder.length;
        map = new HashMap<>();
        for (int i = 0; i < inorder.length; i++) {
            map.put(inorder[i], i);
        }
        return dfs(inorder, postorder, 0, n - 1, 0, n - 1);
    }

    private TreeNode dfs(int[] inorder, int[] postorder, int inorderLeft, int inorderRight,
                         int postOrderLeft, int postOrderRight) {
        if (inorderLeft > inorderRight) {
            return null;
        }
        int root = postorder[postOrderRight];
        TreeNode rootNode = new TreeNode(root);
        int index = map.get(root);
        int leftSubTreeSize = index - inorderLeft;

        rootNode.left = dfs(inorder, postorder, inorderLeft, index - 1,
                postOrderLeft, postOrderLeft + leftSubTreeSize - 1);
        rootNode.right = dfs(inorder, postorder, inorderLeft + leftSubTreeSize + 1, inorderRight,
                postOrderLeft + leftSubTreeSize, postOrderRight - 1);

        return rootNode;
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

思路上，寻找根的节点位置，利用节点值唯一的特性。然后，递归构造树结构。其实也是抄上一道题的思路。