---
title: SpiralMatrixII
date: 2021-04-08 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 螺旋矩阵 II

- [题目介绍](https://yangtzeshore.github.io/2021/04/08/SpiralMatrixII/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/04/08/SpiralMatrixII/#题目解法)

### 题目介绍

#### [螺旋矩阵 II](https://leetcode-cn.com/problems/spiral-matrix-ii/)

给你一个正整数 `n` ，生成一个包含 `1` 到 `n2` 所有元素，且元素按顺时针顺序螺旋排列的 `n x n` 正方形矩阵 `matrix` 。

**示例1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/spiraln.jpg)

```
输入：n = 3
输出：[[1,2,3],[8,9,4],[7,6,5]]
```

**示例2：**

```
输入：n = 1
输出：[[1]]
```

**提示：**

- `1 <= n <= 20`

### 题目解法

```
package algorithm;

public class SpiralMatrixII {

    public static int[][] generateMatrix(int n) {
        int[][] ans = new int[n][n];

        int num = 0;
        int left = 0;
        int right = n - 1;
        int top = 0;
        int bottom = n - 1;
        while (left <= right && top <= bottom) {
            for (int column = left; column <= right; column++) {
                ans[top][column] = ++num;
            }
            for (int row = top + 1; row <= bottom; row++) {
                ans[row][right] = ++num;
            }

            if (left < right && top < bottom) {
                for (int column = right - 1; column > left; column--) {
                    ans[bottom][column] = ++num;
                }
                for (int row = bottom; row > top; row--) {
                    ans[row][left] = ++num;
                }
            }
            left++;
            right--;
            bottom--;
            top++;
        }

        return ans;
    }

    public static void main(String[] args) {
        print(generateMatrix(3));

        print(generateMatrix(1));
    }

    private static void print(int[][] matrix) {
        for (int[] ints : matrix) {
            for (int anInt : ints) {
                System.out.print(anInt);
            }
            System.out.println();
        }
    }
}
```

打印：

```
123
894
765
1
```

**思路：**

思路上，和螺旋矩阵1很类似。