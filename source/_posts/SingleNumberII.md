---
title: SingleNumberII
date: 2022-01-08 09:22:21
tags:
- 
categories:
- Leetcode 
---



# [只出现一次的数字 II](https://leetcode-cn.com/problems/single-number-ii/)

- [题目介绍](https://yangtzeshore.github.io/2022/01/04/SingleNumberII/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2022/01/04/SingleNumberII/#题目解法)

### 题目介绍

#### 只出现一次的数字 II

给你一个整数数组 `nums` ，除某个元素仅出现 **一次** 外，其余每个元素都恰出现 **三次 。**请你找出并返回那个只出现了一次的元素。

**示例 1:**

```
输入：nums = [2,2,3,2]
输出：3
```

**示例 2:**

```
输入：nums = [0,1,0,1,0,1,99]
输出：99
```

**提示：**

- `1 <= nums.length <= 3 * 104`
- `-231 <= nums[i] <= 231 - 1`
- `nums` 中，除某个元素仅出现 **一次** 外，其余每个元素都恰出现 **三次**

**进阶：**你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

#### 题目解法

```
int ones = 0, twos = 0;
for(int num : nums){
    ones = ones ^ num & ~twos;
    twos = twos ^ num & ~ones;
}
return ones;

}
```

打印：

```

```

**思路：**

思路上， 其实就是一个数学分析题，需要利用取余的方式来计算，中间由两个位的状态机来模拟此数据移动，然后找出规律。