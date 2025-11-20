---
title: MagicSquareForming
date: 2025-11-20 19:58:21
tags:
- 
categories:
- hackerrank

---



### Introduction

We define a [magic square](https://en.wikipedia.org/wiki/Magic_square) to be an matrix of distinct positive integers from to where the sum of any row, column, or diagonal of length is always equal to the same number: the *magic constant*.

You will be given a matrix of integers in the inclusive range . We can convert any digit to any other digit in the range at cost of . Given , convert it into a magic square at *minimal* cost. Print this cost on a new line.

**Note:** The resulting magic square must contain distinct integers in the inclusive range .

**Example**

$s = [[5, 3, 4], [1, 5, 8], [6, 4, 2]]

The matrix looks like this:

```
5 3 4
1 5 8
6 4 2
```

We can convert it to the following magic square:

```
8 3 4
1 5 9
6 7 2
```

This took three replacements at a cost of .

**Function Description**

Complete the *formingMagicSquare* function in the editor below.

formingMagicSquare has the following parameter(s):

- *int s[3][3]:* a array of integers

**Returns**

- *int:* the minimal total cost of converting the input square to a magic square

**Input Format**

Each of the lines contains three space-separated integers of row .

**Constraints**

- 

**Sample Input 0**

```
4 9 2
3 5 7
8 1 5
```

**Sample Output 0**

```
1
```

**Explanation 0**

If we change the bottom right value, , from to at a cost of , becomes a magic square at the minimum possible cost.

**Sample Input 1**

```
4 8 2
4 5 7
6 1 6
```

**Sample Output 1**

```
4
```

**Explanation 1**

Using 0-based indexing, if we make

- -> at a cost of 
- -> at a cost of 
- -> at a cost of ,

then the total cost will be .



### Solution

```
package hacker;

import java.io.IOException;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class MagicSquare {

    public static int formingMagicSquare(List<List<Integer>> s) {
        // 3x3 幻方只有 8 种可能的排列组合
        // 所有的行、列、对角线之和必须为 15，且中心必须为 5
        // 816-357-492 834-159-672  然后上下翻转，然后左右翻转再上下
        int[][][] possibilities = {
                {{8, 1, 6}, {3, 5, 7}, {4, 9, 2}},
                {{4, 9, 2}, {3, 5, 7}, {8, 1, 6}},
                {{6, 1, 8}, {7, 5, 3}, {2, 9, 4}},
                {{2, 9, 4}, {7, 5, 3}, {6, 1, 8}},
                {{8, 3, 4}, {1, 5, 9}, {6, 7, 2}},
                {{6, 7, 2}, {1, 5, 9}, {8, 3, 4}},
                {{4, 3, 8}, {9, 5, 1}, {2, 7, 6}},
                {{2, 7, 6}, {9, 5, 1}, {4, 3, 8}}
        };

        int minCost = Integer.MAX_VALUE;

        // 遍历这 8 种可能的幻方
        for (int k = 0; k < 8; k++) {
            int currentCost = 0;

            // 计算将输入矩阵 s 转换为第 k 个幻方的成本
            for (int i = 0; i < 3; i++) {
                for (int j = 0; j < 3; j++) {
                    // 成本 = |输入值 - 目标幻方对应位置的值|
                    currentCost += Math.abs(s.get(i).get(j) - possibilities[k][i][j]);
                }
            }

            // 更新最小成本
            if (currentCost < minCost) {
                minCost = currentCost;
            }
        }

        return minCost;
    }

    public static void main(String[] args) throws IOException {
//        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in));
//        BufferedWriter bufferedWriter = new BufferedWriter(new FileWriter(System.getenv("OUTPUT_PATH")));
//
//        List<List<Integer>> s = new ArrayList<>();
//
//        IntStream.range(0, 3).forEach(i -> {
//            try {
//                s.add(
//                        Stream.of(bufferedReader.readLine().replaceAll("\\s+$", "").split(" "))
//                                .map(Integer::parseInt)
//                                .collect(toList())
//                );
//            } catch (IOException ex) {
//                throw new RuntimeException(ex);
//            }
//        });
//
//        int result = MagicSquare.formingMagicSquare(s);
//
//        bufferedWriter.write(String.valueOf(result));
//        bufferedWriter.newLine();
//
//        bufferedReader.close();
//        bufferedWriter.close();


        List<List<Integer>> s = new ArrayList<>();
        s.add(Arrays.asList(5, 3, 4));
        s.add(Arrays.asList(1, 5, 8));
        s.add(Arrays.asList(6, 4, 2));

        int result = formingMagicSquare(s);
        System.out.println("Minimal total cost: " + result);
    }
}

```

打印：

```
Minimal total cost: 7
```

**思路：**

思路上，有个讨巧的地方，就是魔方的3x3是固定的只有8个；但是如何记忆呢，其实不需要记忆的，因为题目给你了一个完整的魔方示例，然后旋转90°得到4个，然后镜像，得到其余的4个。
