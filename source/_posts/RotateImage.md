---
title: RotateImage
date: 2021-03-14 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 旋转图像

- [题目介绍](https://yangtzeshore.github.io/2021/03/14/RotateImage/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/03/14/RotateImage/#题目解法)

### 题目介绍

#### [旋转图像](https://leetcode-cn.com/problems/rotate-image/)

给定一个 *n* × *n* 的二维矩阵 `matrix` 表示一个图像。请你将图像顺时针旋转 90 度。

你必须在**[ 原地](https://baike.baidu.com/item/原地算法)** 旋转图像，这意味着你需要直接修改输入的二维矩阵。**请不要** 使用另一个矩阵来旋转图像。

**示例1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/mat1.jpg)

```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[[7,4,1],[8,5,2],[9,6,3]]
```

**示例2：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/mat2.jpg)

```
输入：matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]
输出：[[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]
```

**示例 3：**

```
输入：matrix = [[1]]
输出：[[1]]
```

**示例 4：**

```
输入：matrix = [[1,2],[3,4]]
输出：[[3,1],[4,2]]
```

**提示：**

- `matrix.length == n`
- `matrix[i].length == n`
- `1 <= n <= 20`
- `-1000 <= matrix[i][j] <= 1000`

### 题目解法

```
package algorithm;

public class RotateImage {

    public static void rotate(int[][] matrix) {
        int n = matrix.length;
        for (int i = 0; i < n / 2; ++i) {
            for (int j = 0; j < (n + 1) / 2; ++j) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[n - j - 1][i];
                matrix[n - j - 1][i] = matrix[n - i - 1][n - j - 1];
                matrix[n - i - 1][n - j - 1] = matrix[j][n - i - 1];
                matrix[j][n - i - 1] = temp;
            }
        }
    }

    public static void main(String[] args) {
        int[][] matrix = new int[][]{{1,2,3},{4,5,6},{7,8,9}};
        print(matrix);
        rotate(matrix);
        print(matrix);

        matrix = new int[][]{{5,1,9,11},{2,4,8,10},{13,3,6,7}};
        print(matrix);
        rotate(matrix);
        print(matrix);

        matrix = new int[][]{{1}};
        print(matrix);
        rotate(matrix);
        print(matrix);

        matrix = new int[][]{{1,2},{3,4}};
        print(matrix);
        rotate(matrix);
        print(matrix);
    }

    private static void print(int[][] matrix) {
        System.out.println("=====");
        for (int[] ints : matrix) {
            for (int anInt : ints) {
                System.out.print(anInt);
            }
            System.out.println();
        }
        System.out.println("=====");
    }
}
```

打印：

```
=====
123
456
789
=====
=====
741
852
963
=====
=====
51911
24810
13367
=====
=====
132511
34110
6897
=====
=====
1
=====
=====
1
=====
=====
12
34
=====
=====
31
42
=====
```

**思路：**

思路上，当成数学题就好了。然后用个变量承接需要翻转的值。