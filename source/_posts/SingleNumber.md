---
title: SingleNumber
date: 2022-01-02 09:22:21
tags:
- 
categories:
- Leetcode 
---



## [只出现一次的数字](https://leetcode-cn.com/problems/single-number/)

- [题目介绍](https://yangtzeshore.github.io/2022/01/02/SingleNumber/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2022/01/02/SingleNumber/#题目解法)

### 题目介绍

#### 只出现一次的数字

给定一个**非空**整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

**说明：**

你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

**示例 1:**

```
输入: [2,2,1]
输出: 1
```

**示例 2:**

```
输入: [4,1,2,1,2]
输出: 4
```

#### 题目解法

```
package algorithm;

public class SingleNumber {

    public int singleNumber(int[] nums) {
        if (nums.length == 1) {
            return nums[0];
        }
        for (int i = 1; i < nums.length; i++) {
            nums[0] = nums[i] ^ nums[0];
        }
        return nums[0];
    }

    public static void main(String[] args) {
        SingleNumber singleNumber = new SingleNumber();
        int[] nums = new int[]{2,2,1};
        System.out.println(singleNumber.singleNumber(nums));

        nums = new int[]{4,1,2,1,2};
        System.out.println(singleNumber.singleNumber(nums));
    }
}
```

打印：

```
1
4
```

**思路：**

思路上， 就是很简单的数学题。