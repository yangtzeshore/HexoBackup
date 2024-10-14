---
title: SearchA2dMatrix
date: 2021-05-11 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 搜索二维矩阵

- [题目介绍](https://yangtzeshore.github.io/2021/05/11/SearchA2dMatrix/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/05/11/SearchA2dMatrix/#题目解法)

### 题目介绍

#### [搜索二维矩阵](https://leetcode-cn.com/problems/search-a-2d-matrix/)

编写一个高效的算法来判断 `m x n` 矩阵中，是否存在一个目标值。该矩阵具有如下特性：

- 每行中的整数从左到右按升序排列。
- 每行的第一个整数大于前一行的最后一个整数。

**示例1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/mats2d1.jpg)

```
输入：matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3
输出：true
```

**示例2：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/mats2d2.jpg)

```
输入：matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 13
输出：false
```

**提示：**

- `m == matrix.length`
- `n == matrix[0].length`
- `1 <= m, n <= 100`
- −104<=matrix[i][j],target<=104

### 题目解法

```
package algorithm;

public class SearchA2dMatrix {

    public static boolean searchMatrix(int[][] matrix, int target) {

        int row = matrix.length;
        int col = matrix[0].length;

        int rowLow = 0;
        int rowHigh = row - 1;
        int midRow = 0;
        while (rowLow <= rowHigh) {
            midRow = (rowLow + rowHigh) / 2;
            if (matrix[midRow][0] == target) {
                return true;
            } else if (matrix[midRow][0] > target) {
                rowHigh = midRow - 1;
            } else {
                rowLow = midRow + 1;
            }
        }
        if (matrix[midRow][0] > target) {
            midRow -= 1;
        }

        if (midRow < 0) {
            return false;
        }

        int colLeft = 0;
        int colRight = col - 1;
        int midCol;
        while (colLeft <= colRight) {
            midCol = (colLeft + colRight) / 2;
            if (matrix[midRow][midCol] == target) {
                return true;
            } else if (matrix[midRow][midCol] > target) {
                colRight = midCol - 1;
            } else {
                colLeft = midCol + 1;
            }
        }

        return false;
    }

    public static void main(String[] args) {

        int[][] matrix = new int[][]{{1,3,5,7}, {10,11,16,20}, {23,30,34,60}};
        int target = 3;
        System.out.println(searchMatrix(matrix, target));

        matrix = new int[][]{{1,3,5,7}, {10,11,16,20}, {23,30,34,60}};
        target = 13;
        System.out.println(searchMatrix(matrix, target));
    }
}
```

打印：

```
true
false
```

**思路：**

思路很简单，就是二分搜索。