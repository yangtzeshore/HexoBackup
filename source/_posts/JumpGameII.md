---
title: JumpGameII
date: 2021-03-06 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 跳跃游戏 II

- [题目介绍](https://yangtzeshore.github.io/2021/03/06/JumpGameII/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/03/06/JumpGameII/#题目解法)

### 题目介绍

#### [跳跃游戏 II](https://leetcode-cn.com/problems/jump-game-ii/)

给定一个非负整数数组，你最初位于数组的第一个位置。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

你的目标是使用最少的跳跃次数到达数组的最后一个位置。

**示例：**

```
输入: [2,3,1,1,4]
输出: 2
解释: 跳到最后一个位置的最小跳跃数是 2。
     从下标为 0 跳到下标为 1 的位置，跳 1 步，然后跳 3 步到达数组的最后一个位置。
```

**说明:**

假设你总是可以到达数组的最后一个位置。

### 题目解法

```
package algorithm;

public class JumpGameII {

    // 贪心算法
    public static int jump(int[] nums) {
        // 怎么样才能找到每一步的最大
        // 倒过来，算到终点的最小跳数，然后返回第一个数字的值
        int length = nums.length;
        if (length <= 1) {
            return 0;
        }
        int[] stepArray = new int[length - 1];
        for (int i = length - 2; i >= 0; i--) {
            if (nums[i] + i >= length - 1) {
                stepArray[i] = 1;
            } else {
                int value = nums[i];
                int min = Integer.MAX_VALUE - 1;
                for (int j = 1; j <= value; j++) {
                    min = Math.min(min, stepArray[i + j]);
                }
                stepArray[i] = min + 1;
            }
        }

        return stepArray[0];
    }

    public static void main(String[] args) {
        int[] nums = new int[]{2,3,1,1,4};
        System.out.println(jump(nums));

        nums = new int[]{2,3,0,1,4};
        System.out.println(jump(nums));
    }
}
```

打印：

```
2
2
```

**思路：**

思路上，我用的是反向贪心，效率一般。官方的反向贪心效率更差了。正向贪心效率会好一点，也很优雅。