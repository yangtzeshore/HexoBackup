---
title: PascalsTriangle
date: 2021-08-11 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 杨辉三角

- [题目介绍](https://yangtzeshore.github.io/2021/08/11/PascalsTriangle/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/08/11/PascalsTriangle/#题目解法)

### 题目介绍

#### [杨辉三角](https://leetcode-cn.com/problems/pascals-triangle/)

给定一个非负整数 *`numRows`，*生成「杨辉三角」的前 *`numRows`* 行。

在「杨辉三角」中，每个数是它左上方和右上方的数的和。

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/1626927345-DZmfxB-PascalTriangleAnimated2.gif)

**示例 1:**

```
输入: numRows = 5
输出: [[1],[1,1],[1,2,1],[1,3,3,1],[1,4,6,4,1]]
```

**示例 2:**

```
输入: numRows = 1
输出: [[1]]
```

**提示:**

- `1 <= numRows <= 30`

### 题目解法

```
package algorithm;

import java.util.ArrayList;
import java.util.List;

public class PascalsTriangle {

    // 边界条件很好算，就是1，其余的不要看三角形，直接顶格对齐，就会发现规律是自己的位置和减一
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> ans = new ArrayList<>();
        for (int i = 0; i < numRows; i++) {
            List<Integer> rowTemp = new ArrayList<>();
            for (int j = 0; j <= i; j++) {
                if (j == 0 || j == i) {
                    rowTemp.add(1);
                } else {
                    List<Integer> lastRow = ans.get(i - 1);
                    rowTemp.add(lastRow.get(j) + lastRow.get(j - 1));
                }
            }
            ans.add(rowTemp);
        }
        return ans;
    }
}
```

打印：

```

```

**思路：**

思路上，其实很简单。一定要懂杨辉三角的原理。