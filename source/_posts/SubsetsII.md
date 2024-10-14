---
title: SubsetsII
date: 2021-06-19 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 子集 II

- [题目介绍](https://yangtzeshore.github.io/2021/06/19/SubsetsII/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/06/19/SubsetsII/#题目解法)

### 题目介绍

#### [子集 II](https://leetcode-cn.com/problems/subsets-ii/)

给你一个整数数组 `nums` ，其中可能包含重复元素，请你返回该数组所有可能的子集（幂集）。

解集 **不能** 包含重复的子集。返回的解集中，子集可以按 **任意顺序** 排列。

**示例1：**

```
输入：nums = [1,2,2]
输出：[[],[1],[1,2],[1,2,2],[2],[2,2]]
```

**示例 2：**

```
输入：nums = [0]
输出：[[],[0]]
```

### 题目解法

```
package algorithm;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class SubsetsII {

    static List<Integer> t = new ArrayList<>();
    static List<List<Integer>> ans = new ArrayList<>();

    public static List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);
        dfs(false, 0, nums);
        return ans;
    }

    public static void dfs(boolean choosePre, int cur, int[] nums) {
        if (cur == nums.length) {
            ans.add(new ArrayList<>(t));
            return;
        }
        dfs(false, cur + 1, nums);
        if (!choosePre && cur > 0 && nums[cur - 1] == nums[cur]) {
            return;
        }
        t.add(nums[cur]);
        dfs(true, cur + 1, nums);
        t.remove(t.size() - 1);
    }

    public static void main(String[] args) {
        int[] nums = new int[]{1,2,2};
        System.out.println(subsetsWithDup(nums));

        t.clear();
        ans.clear();
        nums = new int[]{0};
        System.out.println(subsetsWithDup(nums));
    }
}
```

打印：

```
[[], [2], [2, 2], [1], [1, 2], [1, 2, 2]]
[[], [0]]
```

**思路：**

思路上，直接copy了官方的深度递归了。位移的解法实在难以理解。