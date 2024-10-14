---
title: Candy
date: 2022-01-01 09:22:21
tags:
- 
categories:
- Leetcode 
---



## [分发糖果](https://leetcode-cn.com/problems/candy/)

- [题目介绍](https://yangtzeshore.github.io/2022/01/01/Candy/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2022/01/01/Candy/#题目解法)

### 题目介绍

#### 分发糖果

`n` 个孩子站成一排。给你一个整数数组 `ratings` 表示每个孩子的评分。

你需要按照以下要求，给这些孩子分发糖果：

- 每个孩子至少分配到 `1` 个糖果。
- 相邻两个孩子评分更高的孩子会获得更多的糖果。

请你给每个孩子分发糖果，计算并返回需要准备的 **最少糖果数目** 。

**示例 1:**

```
输入：ratings = [1,0,2]
输出：5
解释：你可以分别给第一个、第二个、第三个孩子分发 2、1、2 颗糖果。
```

**示例 2:**

```
输入：ratings = [1,2,2]
输出：4
解释：你可以分别给第一个、第二个、第三个孩子分发 1、2、1 颗糖果。
     第三个孩子只得到 1 颗糖果，这满足题面中的两个条件。
```

**提示：**

- `n == ratings.length`
- 1<=n<=2∗104
- 0<=ratings[i]<=2∗104

#### 题目解法

```
package algorithm;

public class Candy {

    public int candy(int[] ratings) {
        int n = ratings.length;
        int[] left = new int[n];
        for (int i = 0; i < n; i++) {
            if (i > 0 && ratings[i] > ratings[i - 1]) {
                left[i] = left[i - 1] + 1;
            } else {
                left[i] = 1;
            }
        }
        int right = 0, ret = 0;
        for (int i = n - 1; i >= 0; i--) {
            if (i < n - 1 && ratings[i] > ratings[i + 1]) {
                right++;
            } else {
                right = 1;
            }
            ret += Math.max(left[i], right);
        }
        return ret;
    }

    public static void main(String[] args) {
        Candy candy = new Candy();
        int[] rating = new int[]{1,0,2};
        System.out.println(candy.candy(rating));

        rating = new int[]{1,2,2};
        System.out.println(candy.candy(rating));

        rating = new int[]{1,3,2,2,1};
        System.out.println(candy.candy(rating));
    }
}
```

打印：

```
5
4
7
```

**思路：**

思路上， 其实蛮简单的，就是将规则拆开，左遍历和右遍历取max。看起来很诡异，其实只要明白不小于1的规则，以及升降的含义就好了。