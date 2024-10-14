---
title: MinimumPathSum
date: 2021-04-20 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 最小路径和

- [题目介绍](https://yangtzeshore.github.io/2021/04/20/MinimumPathSum/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/04/20/MinimumPathSum/#题目解法)

### 题目介绍

#### [最小路径和](https://leetcode-cn.com/problems/minimum-path-sum/)

给定一个包含非负整数的 `m x n` 网格 `grid` ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

**说明：**每次只能向下或者向右移动一步。

**示例1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/minpath.jpg)

```
输入：grid = [[1,3,1],[1,5,1],[4,2,1]]
输出：7
解释：因为路径 1→3→1→1→1 的总和最小。
```

**示例2：**

```
输入：grid = [[1,2,3],[4,5,6]]
输出：12
```

**提示：**

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 200`
- `0 <= grid[i][j] <= 100`

### 题目解法

```
package algorithm;

public class MinimumPathSum {

    public static int minPathSum(int[][] grid) {

        int m = grid.length;
        int n = grid[0].length;
        int[][] score = new int[grid.length][grid[0].length];
        score[0][0] = grid[0][0];

        for (int i = 1; i < m; i++) {
            score[i][0] = score[i - 1][0] + grid[i][0];
        }
        for (int i = 1; i < n; i++) {
            score[0][i] = score[0][i - 1] + grid[0][i];
        }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                score[i][j] = grid[i][j] + Math.min(score[i - 1][j], score[i][j -1]);
            }
        }

        return score[m - 1][n - 1];
    }

    public static void main(String[] args) {
        int[][] grid = new int[][]{{1,3,1}, {1,5,1}, {4,2,1}};
        System.out.println(minPathSum(grid));

        grid = new int[][]{{1,2,3}, {4,5,6}};
        System.out.println(minPathSum(grid));
    }
}
```

打印：

```
7
12
```

**思路：**

思路不是很难，就是写个动态方程求解。