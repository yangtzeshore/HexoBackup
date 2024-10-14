---
title: TwoSum
date: 2020-10-22 09:22:21
tags:
- 
categories:
- Leetcode 
---



## TwoSum

- [题目介绍](https://yangtzeshore.github.io/2020/10/19/TwoSum/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2020/10/19/TwoSum/#题目解法)

### 题目介绍

[两数之和](https://leetcode-cn.com/problems/two-sum/)

给定一个整数数组 `nums` 和一个目标值 `target`，请你在该数组中找出和为目标值的那 **两个** 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

**示例:**

```
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```

## 题目解法

```
public int[] twoSum(int[] nums, int target) {
        int[] indexs = new int[2];
        // k 数值, v 下标
        Map<Integer, Integer> hashMap = new HashMap<Integer, Integer>();

        for (int i = 0; i < nums.length; i++) {
            if (hashMap.containsKey(nums[i])) {
                indexs[0] = hashMap.get(nums[i]);
                indexs[1] = i;
            }
            hashMap.put(target - nums[i], i);
        }
        return indexs;
    }
```

思路：

- 利用隐藏的条件，必定有两个数满足target。
- 那么遍历数字，将补数作为key存入hashmap，如果后面遍历到补数自然就能返回下标。