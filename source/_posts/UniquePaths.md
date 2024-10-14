---
title: UniquePaths
date: 2021-04-16 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 不同路径

- [题目介绍](https://yangtzeshore.github.io/2021/04/16/UniquePaths/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/04/16/UniquePaths/#题目解法)

### 题目介绍

#### [不同路径](https://leetcode-cn.com/problems/unique-paths/)

一个机器人位于一个 `m x n` 网格的左上角 （起始点在下图中标记为 “Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。

问总共有多少条不同的路径？

**示例1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/robot_maze.png)

```
输入：m = 3, n = 7
输出：28
```

**示例2：**

```
输入：m = 3, n = 2
输出：3
解释：
从左上角开始，总共有 3 条路径可以到达右下角。
1. 向右 -> 向下 -> 向下
2. 向下 -> 向下 -> 向右
3. 向下 -> 向右 -> 向下
```

**示例3：**

```
输入：m = 7, n = 3
输出：28
```

**示例4：**

```
输入：m = 3, n = 3
输出：6
```

**提示：**

- `1 <= m, n <= 100`
- 题目数据保证答案小于等于 2∗109

### 题目解法

```
package algorithm;

public class UniquePaths {

    public static int uniquePaths(int m, int n) {
        int[][] path = new int[m][n];
        path[0][0] = 1;
        for (int i = 1; i < m; i++) {
            path[i][0] = 1;
        }
        for (int i = 1; i < n; i++) {
            path[0][i] = 1;
        }

        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                path[i][j] = path[i - 1][j] + path[i][j - 1];
            }
        }
        return path[m - 1][n - 1];
    }

    public static void main(String[] args) {
        System.out.println(uniquePaths(3, 7));

        System.out.println(uniquePaths(3, 2));

        System.out.println(uniquePaths(7, 3));

        System.out.println(uniquePaths(3, 3));
    }
}
```

打印：

```
28
3
28
6
```

**思路：**

思路上，其实很简单。写出动态方程就解决了。不过另外的解法，太过简单，巧妙的是居然这是一道数学题。