---
title: SetMatrixZeroes
date: 2021-05-11 11:22:21
tags:
- 
categories:
- Leetcode 
---



#### 矩阵置零

- [题目介绍](https://yangtzeshore.github.io/2021/05/08/SetMatrixZeroes/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/05/08/SetMatrixZeroes/#题目解法)

### 题目介绍

#### [矩阵置零](https://leetcode-cn.com/problems/set-matrix-zeroes/)

给定一个 `m x n` 的矩阵，如果一个元素为 **0** ，则将其所在行和列的所有元素都设为 **0** 。请使用 **[原地](http://baike.baidu.com/item/原地算法)** 算法。

**进阶：**

- 一个直观的解决方案是使用 `O(m x n)` 的额外空间，但这并不是一个好的解决方案。
- 一个简单的改进方案是使用 `O(m + n)` 的额外空间，但这仍然不是最好的解决方案。
- 你能想出一个仅使用常量空间的解决方案吗？

**示例1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/mat73-1.jpg)

```
输入：matrix = [[1,1,1],[1,0,1],[1,1,1]]
输出：[[1,0,1],[0,0,0],[1,0,1]]
```

**示例2：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/mat73-2.jpg)

```
输入：matrix = [[0,1,2,0],[3,4,5,2],[1,3,1,5]]
输出：[[0,0,0,0],[0,4,5,0],[0,3,1,0]]
```

**提示：**

- `m == matrix.length`
- `n == matrix[0].length`
- `1 <= m, n <= 200`
- −231<=matrix[i][j]<=231−1

### 题目解法

```
package algorithm;

public class SetMatrixZeroes {

    public static void setZeroes(int[][] matrix) {

        int row = matrix.length;
        int col = matrix[0].length;
        boolean colFlag = false;

        for (int i = 0; i < row; i++) {
            // 存第一列是否为0的标志
            if (matrix[i][0] == 0) {
                colFlag = true;
            }

            // 在第一行或者第一列保存判0的标志
            for (int j = 1; j < col; j++) {
                if (matrix[i][j] == 0) {
                    matrix[i][0] = matrix[0][j] = 0;
                }
            }
        }

        // 开始从后往前逆序置0，保证初始化的正确性
        for (int i = row - 1; i >= 0; i--) {
            for (int j = col - 1; j >= 1; j--) {
                if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                    matrix[i][j] = 0;
                }
            }
        }

        // 初始化第一列
        if (colFlag) {
            for (int i = 0; i < row; i++) {
                matrix[i][0] = 0;
            }
        }
    }

    public static void main(String[] args) {
        int[][] matrix = new int[][]{{1,1,1}, {1,0,1}, {1,1,1}};
        setZeroes(matrix);
        print(matrix);

        matrix = new int[][]{{0,1,2,0}, {3,4,5,2}, {1,3,1,5}};
        setZeroes(matrix);
        print(matrix);
    }

    private static void print(int[][] matrix) {
        for (int[] ints : matrix) {
            for (int anInt : ints) {
                System.out.print(anInt);
                System.out.print(" ");
            }
            System.out.println();
        }
    }
}
```

打印：

```
1 0 1 
0 0 0 
1 0 1 
0 0 0 0 
0 4 5 0 
0 3 1 0 
```

**思路：**

思路其实很巧妙，但是题目本身的描述也给了答案，就是常量空间，那一定是要利用数组本身的空间了。