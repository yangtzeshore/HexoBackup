---
title: SpiralMatrix
date: 2021-03-28 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 螺旋矩阵

- [题目介绍](https://yangtzeshore.github.io/2021/03/27/SpiralMatrix/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/03/27/SpiralMatrix/#题目解法)

### 题目介绍

#### [螺旋矩阵](https://leetcode-cn.com/problems/spiral-matrix/)

给你一个 `m` 行 `n` 列的矩阵 `matrix` ，请按照 **顺时针螺旋顺序** ，返回矩阵中的所有元素。

**示例1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/spiral1.jpg)

```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[1,2,3,6,9,8,7,4,5]
```

**示例2：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/spiral.jpg)

```
输入：matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
输出：[1,2,3,4,8,12,11,10,9,5,6,7]
```

**提示：**

- m == matrix.length
- n == matrix[i].length
- 1 <= m, n <= 10
- -100 <= matrix[i][j]<= 100

### 题目解法

```
package algorithm;

import java.util.ArrayList;
import java.util.List;

public class SpiralMatrix {

    public static List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> ans = new ArrayList<>();
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return ans;
        }
        int rows = matrix.length;
        int cols = matrix[0].length;
        int left = 0;
        int right = cols - 1;
        int top = 0;
        int bottom = rows - 1;
        while (left <= right && top <= bottom) {
            for (int column = left; column <= right; column ++) {
                ans.add(matrix[top][column]);
            }
            for (int row = top + 1; row <= bottom; row ++) {
                ans.add(matrix[row][right]);
            }
            if (left < right && top < bottom) {
                for (int column = right - 1; column > left; column --) {
                    ans.add(matrix[bottom][column]);
                }
                for (int row = bottom; row > top; row --) {
                    ans.add(matrix[row][left]);
                }
            }
            top ++;
            right --;
            bottom --;
            left ++;
        }

        return ans;
    }

    public static void main(String[] args) {
        int[][] matrix = new int[][]{{1,2,3},{4,5,6},{7,8,9}};
        System.out.println(spiralOrder(matrix));

        matrix = new int[][] {{1,2,3,4},{5,6,7,8},{9,10,11,12}};
        System.out.println(spiralOrder(matrix));

        matrix = new int[][]{{1,2,3,4,5,6,7,8,9,10},{11,12,13,14,15,16,17,18,19,20}};
        System.out.println(spiralOrder(matrix));
    }
}
```

打印：

```
[1, 2, 3, 6, 9, 8, 7, 4, 5]
[1, 2, 3, 4, 8, 12, 11, 10, 9, 5, 6, 7]
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 20, 19, 18, 17, 16, 15, 14, 13, 12, 11]
```

**思路：**

思路上，就是把一个矩形成一个一个外在的框。