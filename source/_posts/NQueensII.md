---
title: NQueensII
date: 2021-03-23 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### N皇后 II

- [题目介绍](https://yangtzeshore.github.io/2021/03/23/NQueensII/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/03/23/NQueensII/#题目解法)

### 题目介绍

#### [N皇后 II](https://leetcode-cn.com/problems/n-queens-ii/)

**n 皇后问题** 研究的是如何将 `n` 个皇后放置在 `n×n` 的棋盘上，并且使皇后彼此之间不能相互攻击。

给你一个整数 `n` ，返回 **n 皇后问题** 不同的解决方案的数量。

**示例1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/queens.jpg)

```
输入：n = 4
输出：2
解释：如上图所示，4 皇后问题存在两个不同的解法。
```

**示例2：**

```
输入：n = 1
输出：1
```

**提示：**

- `1 <= n <= 9`
- 皇后彼此不能相互攻击，也就是说：任何两个皇后都不能处于同一条横行、纵行或斜线上。

### 题目解法

```
package algorithm;

import java.util.*;

public class NQueensII {

    public static int totalNQueens(int n) {

        int[] queens = new int[n];
        Arrays.fill(queens, -1);
        Set<Integer> columns = new HashSet<>();
        Set<Integer> diagonals1 = new HashSet<>();
        Set<Integer> diagonals2 = new HashSet<>();

        // 从第0行开始
        return track(queens, n, 0, columns, diagonals1, diagonals2);
    }

    private static Integer track(int[] queens, int n, int row,
                              Set<Integer> columns, Set<Integer> diagonals1,
                              Set<Integer> diagonals2) {

        if (row == n) {
            return 1;
        } else {
            int count = 0;
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
                count += track(queens, n, row + 1, columns, diagonals1, diagonals2);
                queens[row] = -1;
                columns.remove(i);
                diagonals1.remove(diagonal1);
                diagonals2.remove(diagonal2);
            }
            return count;
        }
    }

    public static void main(String[] args) {
        System.out.println(totalNQueens(4));

        System.out.println(totalNQueens(1));
    }
}
```

打印：

```
2
1
```

**思路：**

思路上，就是回溯，把上一个的解法稍微改一下。