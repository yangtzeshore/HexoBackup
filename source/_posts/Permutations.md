---
title: Permutations
date: 2021-03-09 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 全排列

- [题目介绍](https://yangtzeshore.github.io/2021/03/09/Permutations/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/03/09/Permutations/#题目解法)

### 题目介绍

#### [全排列](https://leetcode-cn.com/problems/permutations/)

给定一个 **没有重复** 数字的序列，返回其所有可能的全排列。

**示例：**

```
输入: [1,2,3]
输出:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

### 题目解法

```
package algorithm;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Permutations {

    public static List<List<Integer>> permute(int[] nums) {

        List<Integer> output = new ArrayList<>();
        for (int num : nums) {
            output.add(num);
        }
        List<List<Integer>> ans = new ArrayList<>();
        backtrack(output, ans, 0);
        return ans;
    }

    public static void backtrack(List<Integer> output, List<List<Integer>> ans, int first) {
        if (first == output.size()) {
            ans.add(new ArrayList<>(output));
        }
        for (int i = first; i < output.size(); i++) {
            Collections.swap(output, i, first);
            backtrack(output, ans, first + 1);
            Collections.swap(output, i, first);
        }
    }

    public static void main(String[] args) {
        int[] nums = {1,2,3};
        System.out.println(permute(nums));
    }
}
```

打印：

```
[[1, 2, 3], [1, 3, 2], [2, 1, 3], [2, 3, 1], [3, 2, 1], [3, 1, 2]]
```

**思路：**

思路上，使用回溯。