---
title: ThreeSum
date: 2021-01-20 09:22:21
tags:
- 
categories:
- Leetcode 
---



## 三数之和

- [题目介绍](https://yangtzeshore.github.io/2021/01/20/ThreeSum/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/01/20/ThreeSum/#题目解法)

### 题目介绍

[三数之和](https://leetcode-cn.com/problems/3sum/)

给你一个包含 `n` 个整数的数组 `nums`，判断 `nums` 中是否存在三个元素*a，b，c ，*使得 *a + b + c =* 0 ？请你找出所有和为 `0` 且不重复的三元组。

**注意：**答案中不可以包含重复的三元组。

**示例 1:**

```
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
```

**示例 2:**

```
输入：nums = []
输出：[]
```

**示例 3:**

```
输入：nums = [0]
输出：[]
```

**提示：**

- `0 <= nums.length <= 3000`
- `-105 <= nums[i] <= 105`

### 题目解法

```
package algorithm;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class ThreeSum {

    public static List<List<Integer>> threeSum(int[] nums) {

        List<List<Integer>> result = new ArrayList<>();
        if (nums.length < 3) {
            return result;
        }

        Arrays.sort(nums);

        for (int i = 0; i < nums.length - 2; i++) {
            if (nums[i] > 0) {
                break;
            }
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }

            int k = nums.length - 1;
            int target = -nums[i];
            for (int j = i + 1; j < nums.length - 1; j++) {
                if (nums[j] > target) {
                    break;
                }
                if (j > i + 1 && nums[j] == nums[j - 1]) {
                    continue;
                }

                // 缩小范围
                while (j < k && nums[k] + nums[j] > target) {
                    k--;
                }
                // 缩小范围
                if (j == k) {
                    break;
                }
                if (nums[k] + nums[j] == target) {
                    List<Integer> unit = new ArrayList<>();
                    unit.add(nums[i]);
                    unit.add(nums[j]);
                    unit.add(nums[k]);
                    result.add(unit);
                }
            }
        }

            return result;
        }

        public static void main (String[]args){

            int[] nums = new int[]{-1, 0, 1, 2, -1, -4};
            System.out.println(threeSum(nums));

            nums = new int[]{};
            System.out.println(threeSum(nums));

            nums = new int[]{0};
            System.out.println(threeSum(nums));
        }

    }
```

打印：

```
[[-1, -1, 2], [-1, 0, 1]]
[]
[]
```

**思路：**

双指针。