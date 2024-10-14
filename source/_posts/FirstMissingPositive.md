---
title: FirstMissingPositive
date: 2021-02-25 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 缺失的第一个正数

- [题目介绍](https://yangtzeshore.github.io/2021/02/25/FirstMissingPositive/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/02/25/FirstMissingPositive/#题目解法)

### 题目介绍

#### [缺失的第一个正数](https://leetcode-cn.com/problems/first-missing-positive/)

给你一个未排序的整数数组 `nums` ，请你找出其中没有出现的最小的正整数。

**进阶：**你可以实现时间复杂度为 `O(n)` 并且只使用常数级别额外空间的解决方案吗？

**示例 1：**

```
输入：nums = [1,2,0]
输出：3
```

**示例 2：**

```
输入：nums = [3,4,-1,1]
输出：2
```

**示例 3：**

```
输入：nums = [7,8,9,11,12]
输出：1
```

### 题目解法

```
package algorithm;

public class FirstMissingPositive {

    public static int firstMissingPositive1(int[] nums) {
        int length = nums.length;

        // 转成正数，注意用下标，否则不生效
        for (int i = 0; i < length; i++) {
            if (nums[i] <= 0) {
                nums[i] = length + 1;
            }
        }
        for (int i = 0; i < length; i++) {
            int num = Math.abs(nums[i]);
            if (num <= length) {
                nums[num - 1] = -Math.abs(nums[num - 1]);
            }
        }
        for (int i = 0; i < length; i++) {
            if (nums[i] > 0) {
                return i + 1;
            }
        }

        return length + 1;
    }


    public static int firstMissingPositive(int[] nums) {

        int length = nums.length;
        for (int i = 0; i < length; i++) {
            while (nums[i] > 0 && nums[i] < length && nums[nums[i] - 1] != nums[i]) {
                int temp = nums[nums[i] - 1];
                nums[nums[i] - 1] = nums[i];
                nums[i] = temp;
            }
        }

        for (int i = 0; i < length; i++) {
            if (nums[i] != i + 1) {
                return i + 1;
            }
        }
        return length + 1;
    }

    public static void main(String[] args) {

        // 3
        int[] nums = new int[]{1,2,0};
        System.out.println(firstMissingPositive(nums));

        // 2
        nums = new int[]{3,4,-1,1};
        System.out.println(firstMissingPositive(nums));

        // 1
        nums = new int[]{7,8,9,11,12};
        System.out.println(firstMissingPositive(nums));
    }
}
```

打印：

```
3
2
1
```

**思路：**

思路上，自己写了一版，比hash方法的稍微好一点，比两两互换的差了些许。不过还是采纳了官方的解法。考察的其实还是数学功底，算法本身不难。