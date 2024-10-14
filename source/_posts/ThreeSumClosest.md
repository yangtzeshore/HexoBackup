---
title: ThreeSumClosest
date: 2021-01-21 09:22:21
tags:
- 
categories:
- Leetcode 
---



## 最接近的三数之和

- [题目介绍](https://yangtzeshore.github.io/2021/01/21/ThreeSumClosest/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/01/21/ThreeSumClosest/#题目解法)

### 题目介绍

[最接近的三数之和](https://leetcode-cn.com/problems/3sum-closest/)

给定一个包括 *n* 个整数的数组 `nums` 和 一个目标值 `target`。找出 `nums` 中的三个整数，使得它们的和与 `target` 最接近。返回这三个数的和。假定每组输入只存在唯一答案。

**示例 :**

```
输入：nums = [-1,2,1,-4], target = 1
输出：2
解释：与 target 最接近的和是 2 (-1 + 2 + 1 = 2) 。
```

### 题目解法

```
package algorithm;

import java.util.Arrays;

public class ThreeSumClosest {

    public static int threeSumClosest(int[] nums, int target) {

        Arrays.sort(nums);

        int min = 100000;
        for (int i = 0; i < nums.length - 2; i++) {
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }
            int j = i + 1;
            int k = nums.length - 1;
            while (j < k) {
                int temp = nums[i] + nums[j] + nums[k];
                if (temp == target) {
                    return target;
                }
                if (Math.abs(temp - target) < Math.abs(min - target)) {
                    min = temp;
                }
                if (temp > target) {
                    int k1 = k - 1;
                    while (k1 > j && nums[k] == nums[k1]) {
                        k1--;
                    }
                    k = k1;
                } else {
                    int j1 = j + 1;
                    while (j1 < k && nums[j] == nums[j1]) {
                        j1++;
                    }
                    j = j1;
                }

            }
        }

        return min;
    }

    public static void main(String[] args) {

        int[] nums = new int[]{-1,2,1,-4};
        int target = 1;
        System.out.println(threeSumClosest(nums, target));
    }
}
```

打印：

```
2
```

**思路：**

双指针思想。