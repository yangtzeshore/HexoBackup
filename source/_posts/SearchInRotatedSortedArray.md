---
title: SearchInRotatedSortedArray
date: 2021-02-07 09:22:21
tags:
- 
categories:
- Leetcode 
---



## 搜索旋转排序数组

- [题目介绍](https://yangtzeshore.github.io/2021/02/07/SearchInRotatedSortedArray/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/02/07/SearchInRotatedSortedArray/#题目解法)

### 题目介绍

#### [搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

升序排列的整数数组 `nums` 在预先未知的某个点上进行了旋转（例如， `[0,1,2,4,5,6,7]` 经旋转后可能变为 `[4,5,6,7,0,1,2]` ）。

请你在数组中搜索 `target` ，如果数组中存在这个目标值，则返回它的索引，否则返回 `-1` 。

**示例 1：**

```
输入：nums = [4,5,6,7,0,1,2], target = 0
输出：4
```

**示例 2：**

```
输入：nums = [4,5,6,7,0,1,2], target = 3
输出：-1
```

**示例 3：**

```
输入：nums = [1], target = 0
输出：-1
```

### 题目解法

```
package algorithm;

public class SearchInRotatedSortedArray {

    public static int search(int[] nums, int target) {

        int left = 0;
        int right = nums.length - 1;
        while (left <= right) {
            int mid = (left + right) / 2;
            if (nums[mid] == target) {
                return mid;
            } else if (nums[mid] < nums[right]) {// 右边有序
                if (nums[mid] < target && target <= nums[right]) { // 落在右边
                    left = mid + 1;
                } else { // 落在左边
                    right = mid - 1;
                }
            } else {// 左边有序
                if (nums[mid] > target && target >= nums[left]) { // 落在左边
                    right = mid - 1;
                } else { // 落在右边
                    left = mid + 1;
                }
            }
        }

        return -1;
    }

    public static void main(String[] args) {

        int[] nums = new int[]{4,5,6,7,0,1,2};
        int target = 0;
        System.out.println(search(nums, target));

        nums = new int[]{4,5,6,7,0,1,2};
        target = 3;
        System.out.println(search(nums, target));

        nums = new int[]{1};
        target = 0;
        System.out.println(search(nums, target));
    }
}
```

打印：

```
4
-1
-1
```

**思路：**

这道题思路很简单，就是旋转后，怎么判断数据在左边还是右边，然后结合二分查找。