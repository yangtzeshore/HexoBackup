---
title: WordSearch
date: 2021-05-22 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 单词搜索

- [题目介绍](https://yangtzeshore.github.io/2021/05/22/WordSearch/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/05/22/WordSearch/#题目解法)

### 题目介绍

#### [单词搜索](https://leetcode-cn.com/problems/word-search/)

给定一个 `m x n` 二维字符网格 `board` 和一个字符串单词 `word` 。如果 `word` 存在于网格中，返回 `true` ；否则，返回 `false` 。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

**示例1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/word2.jpg)

```
输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
输出：true
```

**示例 2：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/word-1.jpg)

```
输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "SEE"
输出：true
```

**示例3：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/word3.jpg)

```
输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCB"
输出：false
```

**提示：**

- `m == board.length`
- `n = board[i].length`
- `1 <= m, n <= 6`
- `1 <= word.length <= 15`
- `board` 和 `word` 仅由大小写英文字母组成

### 题目解法

```
package algorithm;

public class WordSearch {

    public static boolean exist(char[][] board, String word) {

        int m = board.length;
        int n = board[0].length;
        boolean[][] visited = new boolean[m][n];
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[i].length; j++) {
                if (board[i][j] == word.charAt(0)) {
                    if (dfs(board, visited, word, 0, i, j, m, n)) {
                        return true;
                    }
                }
            }
        }
        return false;
    }

    private static boolean dfs(char[][] board, boolean[][] visited, String word, int index, int row, int col, int m, int n) {
        if ((row < 0 || row >= m) || (col < 0 || col >= n) || visited[row][col]) {
            return false;
        }

        if (word.charAt(index) == board[row][col] && word.length() == index + 1) {
            return true;
        }

        if (word.charAt(index) == board[row][col]) {
            visited[row][col] = true;
            if (dfs(board, visited, word, index + 1, row + 1, col, m, n)) {
                return true;
            }
            if (dfs(board, visited, word, index + 1, row - 1, col, m, n)) {
                return true;
            }
            if (dfs(board, visited, word, index + 1, row, col + 1, m, n)) {
                return true;
            }
            if (dfs(board, visited, word, index + 1, row, col - 1, m, n)) {
                return true;
            }
            visited[row][col] = false;
        }
        return false;
    }

    public static void main(String[] args) {
        char[][] board = new char[][]{{'A','B','C','E'},{'S','F','C','S'},{'A','D','E','E'}};
        String word = "ABCCED";
        System.out.println(exist(board, word));

        board = new char[][]{{'A','B','C','E'},{'S','F','C','S'},{'A','D','E','E'}};
        word = "SEE";
        System.out.println(exist(board, word));

        board = new char[][]{{'A','B','C','E'},{'S','F','C','S'},{'A','D','E','E'}};
        word = "ABCB";
        System.out.println(exist(board, word));
    }
}
```

打印：

```
true
true
false
```

**思路：**

思路很简单。但是因为一直对深度搜索和递归不是很熟，所以意外的一次性通过了。当然效率还是不太高，后续有时间再优化下。