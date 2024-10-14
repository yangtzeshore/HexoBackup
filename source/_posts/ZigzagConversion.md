---
title: Z 字形变换
date: 2021-01-03 09:22:21
tags:
- 
categories:
- Leetcode 
---



## Z 字形变换

- [题目介绍](https://yangtzeshore.github.io/2021/01/03/ZigzagConversion/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/01/03/ZigzagConversion/#题目解法)

### 题目介绍

[Z 字形变换](https://leetcode-cn.com/problems/zigzag-conversion/submissions/)

将一个给定字符串根据给定的行数，以从上往下、从左到右进行 Z 字形排列。

比如输入字符串为 `"LEETCODEISHIRING"` 行数为 3 时，排列如下：

```
L   C   I   R
E T O E S I I G
E   D   H   N
```

之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如：`"LCIRETOESIIGEDHN"`。

请你实现这个将字符串进行指定行数变换的函数：

```
string convert(string s, int numRows);
```

**示例1:**

```
输入: s = "LEETCODEISHIRING", numRows = 3
输出: "LCIRETOESIIGEDHN"
```

**示例2:**

```
输入: s = "LEETCODEISHIRING", numRows = 4
输出: "LDREOEIIECIHNTSG"
解释:

L     D     R
E   O E   I I
E C   I H   N
T     S     G
```

## 

## 题目解法

这一版性能不是很好，看了性能好的解法，是找出了z的排队规律，实现了性能提升。

```
package algorithm;

public class ZigzagConversion {

    public static String convert(String s, int numRows) {
        if (numRows == 1) {
            return s;
        }
        if (s.length() == 1) {
            return s;
        }

        char[][] result = new char[numRows][s.length()];
        for (int i = 0; i < numRows; i++) {
            for (int j = 0; j < s.length(); j++) {
                result[i][j] = ' ';
            }
        }

        int row = 0, col = 0;
        boolean flip = false;
        for (int i = 0; i < s.length(); i++) {

            result[row][col] = s.charAt(i);
            if (flip) {
                row --;
                col ++;
            } else {
                row ++;
            }
            if (row == numRows - 1) {
                flip = true;
            }
            if (row == 0) {
                flip = false;
            }
        }

        StringBuilder stringBuilder = new StringBuilder();
        for (int i = 0; i < numRows; i++) {
            for (int j = 0; j < s.length(); j++) {
                if (result[i][j] != ' ') {
                    stringBuilder.append(result[i][j]);
                }
            }
        }

        return stringBuilder.toString();
    }

    public static void main(String[] args) {

        String s = "LEETCODEISHIRING";
        int numRows = 3;
        System.out.println(convert(s, numRows));

        s = "LEETCODEISHIRING";

        numRows = 4;
        System.out.println(convert(s, numRows));

    }
}
```

思路：

- 就是一字排开，遍历字符串。还可以优化，不需要一个数组作为结果集，直接用拼接的方式承接结果。