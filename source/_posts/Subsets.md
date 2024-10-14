---
title: Subsets
date: 2021-05-20 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 子集

- [题目介绍](https://yangtzeshore.github.io/2021/05/20/Subsets/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/05/20/Subsets/#题目解法)

### 题目介绍

#### [子集](https://leetcode-cn.com/problems/subsets/)

给你一个整数数组 `nums` ，数组中的元素 **互不相同** 。返回该数组所有可能的子集（幂集）。

解集 **不能** 包含重复的子集。你可以按 **任意顺序** 返回解集。

**示例1：**

```
输入：nums = [1,2,3]
输出：[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
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
import java.util.List;

public class Subsets {

    public static List<List<Integer>> subsets(int[] nums) {

        List<List<Integer>> ans = new ArrayList<>();
        ans.add(new ArrayList<>());
        int length = nums.length;
        int max = (int) Math.pow(2, length);
        for (int i = 1; i < max; i++) {
            ans.add(compute(i, nums));
        }
        return ans;
    }

    private static List<Integer> compute(int num, int[] nums) {
        List<Integer> list = new ArrayList<>();
        int count = 0;
        while (num !=0) {
            int temp = num % 2;
            if (temp == 1) {
                list.add(nums[count]);
            }
            count++;
            num /= 2;
        }
        return list;
    }

    private static void print(List<List<Integer>> nums) {
        for (List<Integer> num : nums) {
            for (Integer integer : num) {
                System.out.print(integer + " ");
            }
            System.out.println();
        }
    }

    public static void main(String[] args) {
        int[] nums = new int[]{1, 2, 3};
        print(subsets(nums));

        nums = new int[]{0};
        print(subsets(nums));
    }
}
```

打印：

```
1 
2 
1 2 
3 
1 3 
2 3 
1 2 3 

0 
```

**思路：**

思路很简单，就是数字二进制化，就可以找到所有的组合。