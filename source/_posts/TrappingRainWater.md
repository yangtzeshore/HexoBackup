---
title: TrappingRainWater
date: 2021-02-27 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 接雨水

- [题目介绍](https://yangtzeshore.github.io/2021/02/27/TrappingRainWater/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/02/27/TrappingRainWater/#题目解法)

### 题目介绍

#### [接雨水](https://leetcode-cn.com/problems/trapping-rain-water/)

给定 *n* 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

**示例 1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/rainwatertrap.png)

```
输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
输出：6
解释：上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。 
```

**示例 2：**

```
输入：height = [4,2,0,3,2,5]
输出：9
```

**提示：**

- `n == height.length`
- `0 <= n <= 3 * 104`
- `0 <= height[i] <= 105`

### 题目解法

```
package algorithm;

public class TrappingRainWater {

    // 凹形槽的两个条件：一个是往右找，比自己高的，那就停止；一个是找最近接自己高度的
    // 也就是第二高的，记录位置
    public static int trap(int[] height) {

        int left = 0;
        int right = 1;
        int area = 0;

        while (right < height.length && left < right) {
            if (height[left] < height[right]) {
                left++;
                right = left + 1;
                continue;
            }
            int tempMax = right;
            while (right < height.length && height[left] > height[right]) {
                if (height[right] > height[tempMax]) {
                    tempMax = right;
                }
                right++;
            }
            if (right < height.length && height[right] > height[tempMax]) {
                tempMax = right;
            }
            int minHeight = Math.min(height[left], height[tempMax]);
            for (int i = left + 1; i < tempMax; i++) {
                area += minHeight - height[i];
            }
            left = tempMax;
            right = left + 1;
        }

        return area;
    }

    public static int trap1(int[] height) {
        int left = 0;
        int right = height.length - 1;
        int leftMax = 0;
        int rightMax = 0;
        int ans = 0;

        while (left < right) {
            if (height[left] < height[right]) {
                if (height[left] >= leftMax) {
                    leftMax = height[left];
                } else {
                    ans += leftMax - height[left];
                }
                left++;
            } else {
                if (height[right] >= rightMax) {
                    rightMax = height[right];
                } else {
                    ans += rightMax - height[right];
                }
                right--;
            }
        }

        return ans;
    }

    public static void main(String[] args) {
        int[] height = new int[]{0,1,0,2,1,0,1,3,2,1,2,1};
        System.out.println(trap1(height));

        height = new int[]{4,2,0,3,2,5};
        System.out.println(trap1(height));

        height = new int[]{4,2,3};
        System.out.println(trap1(height));
    }
}
```

打印：

```
6
9
1
```

**思路：**

思路上，自己写了一版，效率一般，也是双指针的变形，不过没有官方的优雅。