---
title: MaximumSubarray
date: 2021-03-27 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 最大子序和

- [题目介绍](https://yangtzeshore.github.io/2021/03/25/MaximumSubarray/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/03/25/MaximumSubarray/#题目解法)

### 题目介绍

#### [最大子序和](https://leetcode-cn.com/problems/maximum-subarray/)

给定一个整数数组 `nums` ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

**示例1：**

```
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。
```

**示例2：**

```
输入：nums = [1]
输出：1
```

**示例 3：**

```
输入：nums = [0]
输出：0
```

**示例4：**

```
输入：nums = [-1]
输出：-1
```

**示例5：**

```
输入：nums = [-100000]
输出：-100000
```

**提示：**

- 1<=nums.length<=3∗104
- −105<=nums[i]<=105

### 题目解法

```
package algorithm;

public class MaximumSubarray {

    public static int maxSubArray(int[] nums) {

        int prev = 0;
        int max = Integer.MIN_VALUE;
        for (int i = 0; i < nums.length; i++) {
            prev = Math.max(prev + nums[i], nums[i]);
            if (max < prev) {
                max = prev;
            }
        }
        return max;
    }

    public static void main(String[] args) {
        int[] nums = new int[]{-2, 1, -3, 4, -1, 2, 1, -5, 4};
        System.out.println(maxSubArray(nums));

        nums = new int[]{1};
        System.out.println(maxSubArray(nums));

        nums = new int[]{0};
        System.out.println(maxSubArray(nums));

        nums = new int[]{-1};
        System.out.println(maxSubArray(nums));

        nums = new int[]{-100000};
        System.out.println(maxSubArray(nums));
    }
}
```

打印：

```
6
1
0
-1
-100000
```

**思路：**

思路上，就是一个简单的动态规划方程，至于发散开来的线段树目前没有写出来。