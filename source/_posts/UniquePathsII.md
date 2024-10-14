---
title: UniquePathsII
date: 2021-04-17 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 不同路径 II

- [题目介绍](https://yangtzeshore.github.io/2021/04/17/UniquePathsII/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/04/17/UniquePathsII/#题目解法)

### 题目介绍

#### [不同路径 II](https://leetcode-cn.com/problems/unique-paths-ii/)

一个机器人位于一个 `m x n` 网格的左上角 （起始点在下图中标记为 “Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。

现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/robot_maze.png)

网格中的障碍物和空位置分别用 `1` 和 `0` 来表示。

**示例1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/robot1.jpg)

```
输入：obstacleGrid = [[0,0,0],[0,1,0],[0,0,0]]
输出：2
解释：
3x3 网格的正中间有一个障碍物。
从左上角到右下角一共有 2 条不同的路径：
1. 向右 -> 向右 -> 向下 -> 向下
2. 向下 -> 向下 -> 向右 -> 向右
```

**示例2：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/robot2.jpg)

```
输入：obstacleGrid = [[0,1],[0,0]]
输出：1
```

**提示：**

- m == obstacleGrid.length
- n == obstacleGrid[i].length
- 1 <= m, n <= 100
- obstacleGrid[i][j] 为 0 或 1

### 题目解法

```
package algorithm;

public class UniquePathsII {

    public static int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int m = obstacleGrid.length;
        int n = obstacleGrid[0].length;

        if (obstacleGrid[0][0] == 1 || obstacleGrid[m - 1][n - 1] == 1) {
            return 0;
        }

        int[][] path = new int[m][n];
        path[0][0] = 1;
        for (int i = 1; i < m; i++) {
            if (obstacleGrid[i][0] == 0) {
                path[i][0] = path[i - 1][0];
            } else {
                path[i][0] = 0;
            }
        }
        for (int i = 1; i < n; i++) {
            if (obstacleGrid[0][i] == 0) {
                path[0][i] = path[0][i - 1];
            } else {
                path[0][i] = 0;
            }
        }

        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (obstacleGrid[i][j] == 1) {
                    path[i][j] = 0;
                } else {
                    path[i][j] = path[i - 1][j] + path[i][j - 1];
                }
            }
        }
        return path[m - 1][n - 1];
    }

    public int uniquePathsWithObstacles1(int[][] obstacleGrid) {
        int n = obstacleGrid.length, m = obstacleGrid[0].length;
        int[] f = new int[m];

        // 存储列，其实存储行也一样，默认的第一列后面的元素不计算，因为本身就是1，
        // 因为有置0的存在，所以不必担心其中有障碍物的情况
        f[0] = obstacleGrid[0][0] == 0 ? 1 : 0;
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < m; ++j) {
                if (obstacleGrid[i][j] == 1) {
                    f[j] = 0;
                    continue;
                }
                // 其实本格子就是左和上，上也就是上个f[j]，左就是f[j-1]，这个需要思考下，所以这是滚动数组
                if (j - 1 >= 0 && obstacleGrid[i][j - 1] == 0) {
                    f[j] += f[j - 1];
                }
            }
        }

        return f[m - 1];
    }

    public static void main(String[] args) {
        int[][] obstacleGrid = new int[][]{{0,0,0},{0,1,0},{0,0,0}};
        System.out.println(uniquePathsWithObstacles(obstacleGrid));

        obstacleGrid = new int[][]{{0,1},{0,0}};
        System.out.println(uniquePathsWithObstacles(obstacleGrid));
    }
}
```

打印：

```
2
1
```

**思路：**

代码给了两种解法，一种是基于上一题的延伸，一种是官方的解法，更加精炼，而且用到了滚动数组。