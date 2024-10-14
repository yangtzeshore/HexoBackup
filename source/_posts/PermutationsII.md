---
title: PermutationsII
date: 2021-03-12 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 全排列 II

- [题目介绍](https://yangtzeshore.github.io/2021/03/12/PermutationsII/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/03/12/PermutationsII/#题目解法)

### 题目介绍

#### [全排列 II](https://leetcode-cn.com/problems/permutations-ii/)

给定一个可包含重复数字的序列 `nums` ，**按任意顺序** 返回所有不重复的全排列。

**示例1：**

```
输入：nums = [1,1,2]
输出：
[[1,1,2],
 [1,2,1],
 [2,1,1]]
```

**示例2：**

```
输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

### 题目解法

```
package algorithm;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class PermutationsII {
    static boolean[] vis;

    public static List<List<Integer>> permuteUnique(int[] nums) {

        List<List<Integer>> ans = new ArrayList<>();
        List<Integer> perm = new ArrayList<>();
        vis = new boolean[nums.length];
        Arrays.sort(nums);
        backtrack(nums, ans, 0, perm);
        return ans;

    }

    public static void backtrack(int[] nums, List<List<Integer>> ans, int idx, List<Integer> perm) {
        if (idx == nums.length) {
            ans.add(new ArrayList<>(perm));
            return;
        }
        for (int i = 0; i < nums.length; ++i) {
            // 从左往右第一个未被填过的数字
            if (vis[i] || (i > 0 && nums[i] == nums[i - 1] && !vis[i - 1])) {
                continue;
            }
            perm.add(nums[i]);
            vis[i] = true;

            backtrack(nums, ans, idx + 1, perm);

            vis[i] = false;
            perm.remove(idx);
        }
    }

    public static void main(String[] args) {
        int[] nums = {1,1,2};
        System.out.println(permuteUnique(nums));

        nums = new int[]{1,2,3};
        System.out.println(permuteUnique(nums));
    }
}
```

打印：

```
[[1, 1, 2], [1, 2, 1], [2, 1, 1]]
[[1, 2, 3], [1, 3, 2], [2, 1, 3], [2, 3, 1], [3, 1, 2], [3, 2, 1]]
```

**思路：**

思路上，使用回溯，排序然后排重。