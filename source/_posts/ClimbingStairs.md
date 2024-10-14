---
title: ClimbingStairs
date: 2021-05-01 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 爬楼梯

- [题目介绍](https://yangtzeshore.github.io/2021/05/01/ClimbingStairs/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/05/01/ClimbingStairs/#题目解法)

### 题目介绍

#### [爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)

假设你正在爬楼梯。需要 *n* 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

**注意：**给定 *n* 是一个正整数。

**示例1：**

```
输入： 2
输出： 2
解释： 有两种方法可以爬到楼顶。
1.  1 阶 + 1 阶
2.  2 阶
```

**示例2：**

```
输入： 3
输出： 3
解释： 有三种方法可以爬到楼顶。
1.  1 阶 + 1 阶 + 1 阶
2.  1 阶 + 2 阶
3.  2 阶 + 1 阶
```

### 题目解法

```
package algorithm;

public class ClimbingStairs {

    public static int climbStairs(int n) {

        // f[n] = f[n-1] + f[n-2]
        if (n == 1) {
            return 1;
        }
        int[] f = new int[n + 1];
        f[0] = 0;
        f[1] = 1;
        f[2] = 2;
        for (int i = 3; i <= n; i++) {
            f[i] = f[i-1] + f[i-2];
        }
        return f[n];
    }

    public static void main(String[] args) {
        System.out.println(climbStairs(2));
        System.out.println(climbStairs(3));
    }
}
```

打印：

```
2
3
```

**思路：**

思路就是一道简单的动态方程。