---
title: FindFirstAndLastPositionOfElementInSortedArray
date: 2021-02-10 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 在排序数组中查找元素的第一个和最后一个位置

- [题目介绍](https://yangtzeshore.github.io/2021/02/10/FindFirstAndLastPositionOfElementInSortedArray/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/02/10/FindFirstAndLastPositionOfElementInSortedArray/#题目解法)

### 题目介绍

#### [在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

给定一个按照升序排列的整数数组 `nums`，和一个目标值 `target`。找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 `target`，返回 `[-1, -1]`。

**进阶：**

- 你可以设计并实现时间复杂度为 `O(log n)` 的算法解决此问题吗？

**示例 1：**

```
输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]
```

**示例 2：**

```
输入：nums = [5,7,7,8,8,10], target = 6
输出：[-1,-1]
```

**示例 3：**

```
输入：nums = [], target = 0
输出：[-1,-1]
```

**提示：**

- 0<=nums.length<=105
- 10−9<=nums[i]<=109
- `nums` 是一个非递减数组
- 10−9<=target<=109

### 题目解法

```
package algorithm;

import java.util.Arrays;

public class FindFirstAndLastPositionOfElementInSortedArray {

    public static int[] searchRange(int[] nums, int target) {

        int[] result;

        int length = nums.length;
        int left = 0;
        int right = length - 1;
        while (left <= right) {
            int mid = (left + right) / 2;
            if (nums[mid] == target) {
                left = right = mid;
                while (--left >= 0 && nums[left] == target) {

                }
                left++;
                while (++right < length && nums[right] == target) {

                }
                right--;
                result = new int[]{left, right};
                return result;
            } else if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }

        result = new int[]{-1, -1};
        return result;
    }

    public static void main(String[] args) {

        //nums = [5,7,7,8,8,10], target = 8
        int[] nums = new int[]{5,7,7,8,8,10};
        int target = 8;
        System.out.println(Arrays.toString(searchRange(nums, target)));

        //nums = [5,7,7,8,8,10], target = 6
        nums = new int[]{5,7,7,8,8,10};
        target = 6;
        System.out.println(Arrays.toString(searchRange(nums, target)));

        //nums = [], target = 0
        nums = new int[]{};
        target = 0;
        System.out.println(Arrays.toString(searchRange(nums, target)));
    }

}
```

打印：

```
[3, 4]
[-1, -1]
[-1, -1]
```

**思路：**

这道题思路很简单，就是二分查找，然后扩展边界。