---
title: NQueens
date: 2021-03-21 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### N 皇后

- [题目介绍](https://yangtzeshore.github.io/2021/03/21/NQueens/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/03/21/NQueens/#题目解法)

### 题目介绍

#### [N 皇后](https://leetcode-cn.com/problems/n-queens/)

**n 皇后问题** 研究的是如何将 `n` 个皇后放置在 `n×n` 的棋盘上，并且使皇后彼此之间不能相互攻击。

给你一个整数 `n` ，返回所有不同的 **n 皇后问题** 的解决方案。

每一种解法包含一个不同的 **n 皇后问题** 的棋子放置方案，该方案中 `'Q'` 和 `'.'` 分别代表了皇后和空位。

**示例1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/queens.jpg)

```
输入：n = 4
输出：[[".Q..","...Q","Q...","..Q."],["..Q.","Q...","...Q",".Q.."]]
解释：如上图所示，4 皇后问题存在两个不同的解法。
```

**示例2：**

```
输入：n = 1
输出：[["Q"]]
```

**提示：**

- `1 <= n <= 9`
- 皇后彼此不能相互攻击，也就是说：任何两个皇后都不能处于同一条横行、纵行或斜线上。

### 题目解法

```
package algorithm;

import java.util.*;

public class NQueens {

    public static List<List<String>> solveNQueens(int n) {
        List<List<String>> ans = new ArrayList<>();
        int[] queens = new int[n];
        Arrays.fill(queens, -1);
        Set<Integer> columns = new HashSet<>();
        Set<Integer> diagonals1 = new HashSet<>();
        Set<Integer> diagonals2 = new HashSet<>();

        // 从第0行开始
        track(ans, queens, n, 0, columns, diagonals1, diagonals2);

        return ans;
    }

    private static void track(List<List<String>> ans, int[] queens, int n, int row,
                                            Set<Integer> columns, Set<Integer> diagonals1,
                                            Set<Integer> diagonals2) {

        if (row == n) {
            List<String> strings = generateBoard(n, queens);
            ans.add(strings);
        } else {
            // 每一行都要从第0列开始遍历
            for (int i = 0; i < n; i++) {
                if (columns.contains(i)) {
                    continue;
                }
                int diagonal1 = row - i;
                if (diagonals1.contains(diagonal1)) {
                    continue;
                }
                int diagonal2 = row + i;
                if (diagonals2.contains(diagonal2)) {
                    continue;
                }
                queens[row] = i;
                columns.add(i);
                diagonals1.add(diagonal1);
                diagonals2.add(diagonal2);
                track(ans, queens, n, row + 1, columns, diagonals1, diagonals2);
                queens[row] = -1;
                columns.remove(i);
                diagonals1.remove(diagonal1);
                diagonals2.remove(diagonal2);
            }
        }
    }

    private static List<String> generateBoard(int n, int[] queens) {
        List<String> result = new ArrayList<>();

        for (int i = 0; i < n; i++) {
            char[] row = new char[n];
            Arrays.fill(row, '.');
            row[queens[i]] = 'Q';
            String s = new String(row);
            result.add(s);
        }
        return result;
    }

    public static void main(String[] args) {
        System.out.println(solveNQueens(4));

        System.out.println(solveNQueens(1));
    }
}
```

打印：

```
[[.Q.., ...Q, Q..., ..Q.], [..Q., Q..., ...Q, .Q..]]
[[Q]]
```

**思路：**

思路上，就是回溯。