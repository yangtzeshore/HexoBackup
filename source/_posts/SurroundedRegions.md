---
title: SurroundedRegions
date: 2021-09-16 10:22:21
tags:
- 
categories:
- Leetcode 
---



#### [被围绕的区域](https://leetcode-cn.com/problems/surrounded-regions/)

- [题目介绍](https://yangtzeshore.github.io/2021/09/16/SurroundedRegions/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/09/16/SurroundedRegions/#题目解法)

### 题目介绍

#### 被围绕的区域

给你一个 `m x n` 的矩阵 `board` ，由若干字符 `'X'` 和 `'O'` ，找到所有被 `'X'` 围绕的区域，并将这些区域里所有的 `'O'` 用 `'X'` 填充。

**示例 1:**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/xogrid.jpg)

```
输入：board = [["X","X","X","X"],["X","O","O","X"],["X","X","O","X"],["X","O","X","X"]]
输出：[["X","X","X","X"],["X","X","X","X"],["X","X","X","X"],["X","O","X","X"]]
解释：被围绕的区间不会存在于边界上，换句话说，任何边界上的 'O' 都不会被填充为 'X'。 任何不在边界上，或不与边界上的 'O' 相连的 'O' 最终都会被填充为 'X'。如果两个元素在水平或垂直方向相邻，则称它们是“相连”的。 
```

**示例 2:**

```
输入：board = [["X"]]
输出：[["X"]]
```

**提示：**

- `m == board.length`
- `n == board[i].length`
- `1 <= m, n <= 200`
- `board[i][j]` 为 `'X'` 或 `'O'`

### 题目解法

```
 int m;
    int n;
    public void solve(char[][] board) {

        m = board.length; // 行
        n = board[0].length; // 列
if (m == 1 || n == 1) {
            return;
        }
        for (int i = 0; i < n; i++) {
            dfs(board, 0, i);
            dfs(board, m - 1, i);
        }
        for (int i = 1; i < m - 1; i++) {
            dfs(board, i, 0);
            dfs(board, i, n - 1);
        }
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == 'A') {
                    board[i][j] = 'O';
                } else if (board[i][j] == 'O') {
                    board[i][j] = 'X';
                }
            }
        }

    }

    private void dfs(char[][] board, int x, int y) {
        if (x < 0 || x > m - 1 || y < 0 || y > n - 1 || board[x][y] != 'O') {
            return;
        }
        board[x][y] = 'A';
        dfs(board, x + 1, y);
        dfs(board, x - 1, y);
        dfs(board, x, y + 1);
        dfs(board, x, y - 1);
    }
```

打印：

```

```

**思路：**

思路上， 注意dfs的结束条件是不等于O，因为边界可能被来回dfs，O会被标记为A，不会来回标记。