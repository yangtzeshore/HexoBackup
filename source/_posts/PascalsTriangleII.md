---
title: PascalsTriangleII
date: 2021-08-14 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 杨辉三角 II

- [题目介绍](https://yangtzeshore.github.io/2021/08/14/PascalsTriangleII/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/08/14/PascalsTriangleII/#题目解法)

### 题目介绍

#### [杨辉三角 II](https://leetcode-cn.com/problems/pascals-triangle-ii/)

给定一个非负索引 `rowIndex`，返回「杨辉三角」的第 `rowIndex` 行。

在「杨辉三角」中，每个数是它左上方和右上方的数的和。

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/1626927345-DZmfxB-PascalTriangleAnimated2.gif)

**示例 1:**

```
输入: rowIndex = 3
输出: [1,3,3,1]
```

**示例 2:**

```
输入: rowIndex = 0
输出: [1]
```

**示例 3:**

```
输入: rowIndex = 1
输出: [1,1]
```

**提示:**

- `1 <= numRows <= 33`

### 题目解法

```
package algorithm;

import java.util.ArrayList;
import java.util.List;

public class PascalsTriangleII {

    public static List<Integer> getRow(int rowIndex) {

        List<Integer> prev = new ArrayList<>();
        prev.add(1);
        List<Integer> cur = new ArrayList<>();

        int i = 0;
        while (i <= rowIndex) {
            cur = new ArrayList<>();
            int j = 0;
            while (j <= i) {
                if (j <= 0 || j == i) {
                    cur.add(prev.get(0));
                } else {
                    cur.add(prev.get(j) + prev.get(j - 1));
                }
                j++;
            }
            i++;
            prev = cur;
        }
        return cur;
    }

    public static void main(String[] args) {
        System.out.println(getRow(3));
    }
}
```

打印：

```

```

**思路：**

思路上，其实很简单。一定要懂杨辉三角的原理。