---
title: CombinationSumII
date: 2021-02-23 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 组合总和 II

- [题目介绍](https://yangtzeshore.github.io/2021/02/23/CombinationSumII/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/02/23/CombinationSumII/#题目解法)

### 题目介绍

#### [组合总和 II](https://leetcode-cn.com/problems/combination-sum-ii/)

给定一个数组 `candidates` 和一个目标数 `target` ，找出 `candidates` 中所有可以使数字和为 `target` 的组合。

`candidates` 中的每个数字在每个组合中只能使用一次。

**说明：**

- 所有数字（包括目标数）都是正整数。
- 解集不能包含重复的组合。

**示例 1：**

```
输入: candidates = [10,1,2,7,6,1,5], target = 8,
所求解集为:
[
  [1, 7],
  [1, 2, 5],
  [2, 6],
  [1, 1, 6]
]
```

**示例 2：**

```
输入: candidates = [2,5,2,1,2], target = 5,
所求解集为:
[
  [1,2,2],
  [5]
]
```

### 题目解法

```
package algorithm;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class CombinationSumII {

    static List<int []> freq = new ArrayList<>();
    static List<List<Integer>> ans = new ArrayList<>();
    static List<Integer> sequence = new ArrayList<>();

    public static List<List<Integer>> combinationSum2(int[] candidates, int target) {
        Arrays.sort(candidates);

        for (int candidate : candidates) {
            if (freq.isEmpty() || freq.get(freq.size() - 1)[0] != candidate) {
                freq.add(new int[]{candidate, 1});
            } else {
                ++freq.get(freq.size() - 1)[1];
            }
        }
        dfs(0, target);
        return ans;
    }

    private static void dfs(int pos, int target) {
        if (target == 0) {
            ans.add(new ArrayList<>(sequence));
        }
        if (pos == freq.size() || target < freq.get(pos)[0]) {
            return;
        }
        // 跳过
        dfs(pos + 1, target);

        int most = Math.min(target / freq.get(pos)[0], freq.get(pos)[1]);
        for (int i = 1; i <= most; i++) {
            sequence.add(freq.get(pos)[0]);
            // 虽然每次都会循环，但是pos都不变，也就是每次从下一个为为孩子开始，但是重复数字会叠加
            dfs(pos + 1, target - i * freq.get(pos)[0]);
        }
        for (int i = 1; i <= most; i++) {
            sequence.remove(sequence.size() - 1);
        }
    }

    public static void main(String[] args) {

        int[] candidates = new int[]{10, 1, 2, 7, 6, 1, 5};
        int targe = 8;
        System.out.println(combinationSum2(candidates, targe));

        candidates = new int[]{2, 5, 2, 1, 2};
        targe = 5;
        System.out.println(combinationSum2(candidates, targe));
    }
}
```

打印：

```
[[2, 6], [1, 7], [1, 2, 5], [1, 1, 6]]
[[2, 6], [1, 7], [1, 2, 5], [1, 1, 6], [5]]
```

**思路：**

思路也很简单，就是搜索回溯。注意的是，不能有重复。