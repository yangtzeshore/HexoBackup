---
title: SearchInsertPosition
date: 2021-02-12 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 搜索插入位置

- [题目介绍](https://yangtzeshore.github.io/2021/02/12/SearchInsertPosition/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/02/12/SearchInsertPosition/#题目解法)

### 题目介绍

#### [搜索插入位置](https://leetcode-cn.com/problems/search-insert-position/)

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

你可以假设数组中无重复元素。

**示例 1：**

```
输入: [1,3,5,6], 5
输出: 2
```

**示例 2：**

```
输入: [1,3,5,6], 2
输出: 1
```

**示例 3：**

```
输入: [1,3,5,6], 7
输出: 4
```

**示例 3：**

```
输入: [1,3,5,6], 0
输出: 0
```

### 题目解法

```
package algorithm;

public class SearchInsertPosition {

    public static int searchInsert(int[] nums, int target) {

        if (nums.length == 0 || target < nums[0]) {
            return 0;
        }

        int length = nums.length;
        if (target > nums[length - 1]) {
            return length;
        }

        int left = 0;
        int right = length - 1;
        while (left <= right) {
            if (target == nums[left]) {
                return left;
            }
            if (target == nums[right]) {
                return right;
            }
            int mid = (left + right) / 2;
            if (target == nums[mid]) {
                return mid;
            }
            if (target > nums[mid]) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return left;
    }

    public static void main(String[] args) {

        //输入: [1,3,5,6], 5
        //输出: 2
        int[] nums = new int[]{1,3,5,6};
        int target = 5;
        System.out.println(searchInsert(nums, target));

        //输入: [1,3,5,6], 2
        //输出: 1
        nums = new int[]{1,3,5,6};
        target = 2;
        System.out.println(searchInsert(nums, target));

        //输入: [1,3,5,6], 7
        //输出: 4
        nums = new int[]{1,3,5,6};
        target = 7;
        System.out.println(searchInsert(nums, target));

        //输入: [1,3,5,6], 0
        //输出: 0
        nums = new int[]{1,3,5,6};
        target = 0;
        System.out.println(searchInsert(nums, target));

        //[1,3,5]
        //4
        nums = new int[]{1,3,5};
        target = 4;
        System.out.println(searchInsert(nums, target));
    }
}
```

打印：

```
2
1
4
0
2
```

**思路：**

这道题思路很简单，就是二分查找，不过官方的解答比较优雅。还有就是这个除法：((right−left)>>1)+left。