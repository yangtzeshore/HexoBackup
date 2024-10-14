---
title: JumpGame
date: 2021-03-28 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 跳跃游戏

- [题目介绍](https://yangtzeshore.github.io/2021/03/28/JumpGame/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/03/28/JumpGame/#题目解法)

### 题目介绍

#### [跳跃游戏](https://leetcode-cn.com/problems/jump-game/)

给定一个非负整数数组 `nums` ，你最初位于数组的 **第一个下标** 。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个下标。

**示例1：**

```
输入：nums = [2,3,1,1,4]
输出：true
解释：可以先跳 1 步，从下标 0 到达下标 1, 然后再从下标 1 跳 3 步到达最后一个下标。
```

**示例2：**

```
输入：nums = [3,2,1,0,4]
输出：false
解释：无论怎样，总会到达下标为 3 的位置。但该下标的最大跳跃长度是 0 ， 所以永远不可能到达最后一个下标。
```

**提示：**

- 1<=nums.length<=3∗104
- 0<=nums[i]<=105

### 题目解法

```
package algorithm;

public class JumpGame {

    public static boolean canJump(int[] nums) {

        int last = nums.length - 1;
        int lastFlag = last;
        boolean[] flags = new boolean[nums.length + 1];
        for (int i = last; i >= 0; i--) {
            if (i + nums[i] >= lastFlag) {
                flags[i] = true;
                lastFlag = i;
            }
        }

        return flags[0];
    }

    public static void main(String[] args) {
        int[] nums = new int[]{2,3,1,1,4};
        System.out.println(canJump(nums));

        nums = new int[]{3,2,1,0,4};
        System.out.println(canJump(nums));
    }
}
```

打印：

```
true
false
```

**思路：**

思路上，就是一个简单的动态规划方程。官方的贪吃当然效率会更好一点。