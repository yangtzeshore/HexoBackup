---
title: UniqueBinarySearchTrees
date: 2021-07-01 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 不同的二叉搜索树

- [题目介绍](https://yangtzeshore.github.io/2021/07/01/UniqueBinarySearchTrees/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/07/01/UniqueBinarySearchTrees/#题目解法)

### 题目介绍

#### [不同的二叉搜索树](https://leetcode-cn.com/problems/unique-binary-search-trees/)

给你一个整数 `n` ，求恰由 `n` 个节点组成且节点值从 `1` 到 `n` 互不相同的 **二叉搜索树** 有多少种？返回满足题意的二叉搜索树的种数。

**示例 1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/uniquebstn3.jpg)

```
输入：n = 3
输出：5
```

**示例 2：**

```
输入：n = 1
输出：1
```

**提示：**

- `1 <= n <= 19`

### 题目解法

```
package algorithm;

public class UniqueBinarySearchTrees {

    public static int numTrees(int n) {

        int[] G = new int[n + 1];
        G[0] = 1;
        G[1] = 1;

        for (int i = 2; i <= n; i++) {
            for (int j = 1; j <= i; j++) {
                G[i] += G[j - 1] * G[i - j];
            }
        }
        return G[n];
    }

    public static void main(String[] args) {

        System.out.println(numTrees(3));

        System.out.println(numTrees(1));
    }
}
```

打印：

```
5
1
```

**思路：**

思路上，动态规划了。数学确实是工具，官方题解二的卡特兰数确实也厉害。