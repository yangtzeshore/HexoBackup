---
title: Combinations
date: 2021-05-15 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 组合

- [题目介绍](https://yangtzeshore.github.io/2021/05/15/Combinations/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/05/15/Combinations/#题目解法)

### 题目介绍

#### [组合](https://leetcode-cn.com/problems/combinations/)

给定两个整数 *n* 和 *k*，返回 1 … *n* 中所有可能的 *k* 个数的组合。

**示例1：**

```
输入: n = 4, k = 2
输出:
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

### 题目解法

```
package algorithm;

import java.util.ArrayList;
import java.util.List;

public class Combinations {

    static List<Integer> unit = new ArrayList<>();
    static List<List<Integer>> ans = new ArrayList<>();

    public static List<List<Integer>> combine(int n, int k) {
        dfs(1, n, k);
        return ans;
    }

    private static void dfs(int cur, int n, int k) {

        if (unit.size()  + (n - cur + 1) < k) {
            return;
        }
        if (unit.size() == k) {
            ans.add(new ArrayList<>(unit));
            return;
        }

        unit.add(cur);
        dfs(cur + 1, n, k);
        unit.remove(unit.size() - 1);
        dfs(cur + 1, n, k);
    }

    public static void main(String[] args) {
        print(combine(4, 2));
    }

    private static void print(List<List<Integer>> ans) {
        for (List<Integer> an : ans) {
            for (Integer integer : an) {
                System.out.print(integer);
                System.out.print(" ");
            }
            System.out.println();
        }
    }
}
```

打印：

```
1 2 
1 3 
1 4 
2 3 
2 4 
3 4 
```

**思路：**

思路深度递归很简单，官方给出的另外的解法其实很巧妙，不过最近忙练车，没时间研究了。