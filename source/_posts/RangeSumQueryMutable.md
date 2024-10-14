---
title: RangeSumQueryMutable
date: 2024-10-14 08:58:31
tags:
- 
categories:
- Leetcode 
---



## RangeSumQueryMutable

### 题目介绍

[307. 区域和检索 - 数组可修改](https://leetcode.cn/problems/range-sum-query-mutable/description/)

给你一个数组 `nums` ，请你完成两类查询。

1. 其中一类查询要求 **更新** 数组 `nums` 下标对应的值
2. 另一类查询要求返回数组 `nums` 中索引 `left` 和索引 `right` 之间（ **包含** ）的nums元素的 **和** ，其中 `left <= right`

实现 `NumArray` 类：

- `NumArray(int[] nums)` 用整数数组 `nums` 初始化对象
- `void update(int index, int val)` 将 `nums[index]` 的值 **更新** 为 `val`
- `int sumRange(int left, int right)` 返回数组 `nums` 中索引 `left` 和索引 `right` 之间（ **包含** ）的nums元素的 **和** （即，`nums[left] + nums[left + 1], ..., nums[right]`）

 

**示例 1：**

```
输入：
["NumArray", "sumRange", "update", "sumRange"]
[[[1, 3, 5]], [0, 2], [1, 2], [0, 2]]
输出：
[null, 9, null, 8]

解释：
NumArray numArray = new NumArray([1, 3, 5]);
numArray.sumRange(0, 2); // 返回 1 + 3 + 5 = 9
numArray.update(1, 2);   // nums = [1,2,5]
numArray.sumRange(0, 2); // 返回 1 + 2 + 5 = 8
```

 

**提示：**

- `1 <= nums.length <= 3 * 104`
- `-100 <= nums[i] <= 100`
- `0 <= index < nums.length`
- `-100 <= val <= 100`
- `0 <= left <= right < nums.length`
- 调用 `update` 和 `sumRange` 方法次数不大于 `3 * 104` 



### 题目解法

```
public class NumArray {

    private int[] nums;
    private int[] sum;
    private int size;

    public NumArray(int[] nums) {
        this.nums = nums;
        size = (int) Math.sqrt(nums.length);
        sum = new int[(nums.length + size - 1) / size];
        for (int i = 0; i < nums.length; i++) {
            sum[i/size] += nums[i];
        }
    }

    public void update(int index, int val) {
        sum[index/size] += val - nums[index];
        nums[index] = val;
    }

    public int sumRange(int left, int right) {
        int b1 = left / size, i1 = left % size, b2 = right / size, i2 = right % size;
        if (b1 == b2) {
            int result = 0;
            for (int i = i1; i <= i2; i++) {
                result += nums[b1 * size + i];
            }
            return result;
        }
        int result1 = 0;
        for (int i = i1; i < size; i++) {
            result1 += nums[b1 * size + i];
        }
        int result2 = 0;
        for (int i = 0; i <= i2; i++) {
            result2 += nums[b2 * size + i];
        }
        int result3 = 0;
        for (int i = b1 + 1; i < b2; i++) {
            result2 += sum[i];
        }
        return result1 + result2 + result3;
    }


}
```

打印：

```
```



### 思路

官方给出了三种解决思路：分区间，线状数组，线段树。这道题在ACM应该是入门题，所以感觉leetcode更像应付互联网面试，多练习ACM对于算法和编码的提升还是有的，有空还是多去ACM OJ刷一刷。

​	