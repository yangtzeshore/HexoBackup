---
title: BinaryTreeInorderTraversal
date: 2021-06-27 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 二叉树的中序遍历

- [题目介绍](https://yangtzeshore.github.io/2021/06/27/BinaryTreeInorderTraversal/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/06/27/BinaryTreeInorderTraversal/#题目解法)

### 题目介绍

#### [二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

给定一个二叉树的根节点 `root` ，返回它的 **中序** 遍历。

**示例 1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/inorder_1.jpg)

```
输入：root = [1,null,2,3]
输出：[1,3,2]
```

**示例 2：**

```
输入：root = []
输出：[]
```

**示例 3：**

```
输入：root = [1]
输出：[1]
```

**示例 4：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/inorder_4.jpg)

```
输入：root = [1,2]
输出：[2,1]
```

**示例 5：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/inorder_5.jpg)

```
输入：root = [1,null,2]
输出：[1,2]
```

**提示：**

- 树中节点数目在范围 `[0, 100]` 内
- `-100 <= Node.val <= 100`

### 题目解法

```
package algorithm;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class BinaryTreeInorderTraversal {

    static List<Integer> ans = new ArrayList<>();
    public static List<Integer> inorderTraversal(TreeNode root) {
        dfs(root);
        return ans;
    }

    private static void dfs(TreeNode root) {
        if (root == null) {
            return;
        }
        dfs(root.left);
        ans.add(root.val);
        dfs(root.right);
    }

    public static void main(String[] args) {
        TreeNode n1 = new TreeNode(1);
        TreeNode n2 = new TreeNode(2);
        n1.left = null;
        n1.right = n2;
        TreeNode n3 = new TreeNode(3);
        n2.left = n3;
        System.out.println(Arrays.toString(inorderTraversal(n1).toArray()));
        ans.clear();

        TreeNode n4 = null;
        System.out.println(Arrays.toString(inorderTraversal(n4).toArray()));
        ans.clear();

        TreeNode n5 = new TreeNode(1);
        System.out.println(Arrays.toString(inorderTraversal(n5).toArray()));
        ans.clear();

        TreeNode n6 = new TreeNode(1);
        TreeNode n7 = new TreeNode((2));
        n6.left = n7;
        System.out.println(Arrays.toString(inorderTraversal(n6).toArray()));
        ans.clear();

        TreeNode n8 = new TreeNode(1);
        TreeNode n9 = new TreeNode(2);
        n8.right = n9;
        System.out.println(Arrays.toString(inorderTraversal(n8).toArray()));
        ans.clear();
    }

    static class TreeNode {
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
[1, 3, 2]
[]
[1]
[2, 1]
[1, 2]
```

**思路：**

思路上，太简单了。我决定下次面试的时候，让候选者写一个。确实不难，而且也能判断这个人是否写过代码。