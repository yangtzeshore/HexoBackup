---
title: FindMedianSortedArrays
date: 2021-01-01 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 题目介绍

[寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

给定两个大小为 m 和 n 的正序（从小到大）数组 nums1 和 nums2。请你找出并返回这两个正序数组的中位数。

进阶：你能设计一个时间复杂度为 O(log (m+n)) 的算法解决此问题吗？

示例 1:

```
输入：nums1 = [1,3], nums2 = [2]
输出：2.00000
解释：合并数组 = [1,2,3] ，中位数 2
```

示例 2:

```
输入：nums1 = [1,2], nums2 = [3,4]
输出：2.50000
解释：合并数组 = [1,2,3,4] ，中位数 (2 + 3) / 2 = 2.5
```

示例 3:

```
输入：nums1 = [0,0], nums2 = [0,0]
输出：0.00000
```

示例 4:

```
输入：nums1 = [], nums2 = [1]
输出：1.00000
```

示例 5:

```
输入：nums1 = [2], nums2 = []
输出：2.00000
```

## 题目解法

1、下面第一版自己写的，比较粗糙

```
package algorithm;

import java.util.Arrays;

public class FindMedianSortedArrays {

    public static double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int [] nums3 = new int[nums1.length + nums2.length];
        if (nums2.length == 0 && nums1.length == 0) {
            return 0;
        }
        if (nums1.length == 0) {
            return getMidNum(nums2);
        }
        if (nums2.length == 0) {
            return getMidNum(nums1);
        }
        for (int i = 0, j = 0, k = 0; ;) {
            if (nums1[i] < nums2[j]) {
                nums3[k] = nums1[i];
                i++;
                k++;
            } else if (nums1[i] > nums2[j]) {
                nums3[k] = nums2[j];
                j++;
                k++;
            } else if (nums1[i] == nums2[j]) {
                nums3[k] = nums2[j];
                j++;
                k++;
            }
            if (i == nums1.length) {
                for (; j < nums2.length; j++, k++) {
                    nums3[k] = nums2[j];
                }
                break;
            }
            if (j == nums2.length) {
                for (; i < nums1.length; i++, k++) {
                    nums3[k] = nums1[i];
                }
                break;
            }
        }
        System.out.println(Arrays.toString(nums3));
        return getMidNum(nums3);
    }

    private static double getMidNum(int[] nums3) {
        if(nums3.length % 2 == 1) {
            return nums3[(nums3.length - 1)/2];
        } else {
            return (nums3[(nums3.length - 1)/2] + nums3[(nums3.length)/2]) / 2.0;
        }
    }

    public static void main(String[] args) {
        int[] nums1 = new int[]{1, 3}, nums2 = new int[]{2};
        System.out.println(findMedianSortedArrays(nums1, nums2));
        int[] nums3 = new int[]{1,2}, nums4 = new int[]{3,4};
        System.out.println(findMedianSortedArrays(nums3, nums4));
        int[] nums5 = new int[]{0,0}, nums6 = new int[]{0,0};
        System.out.println(findMedianSortedArrays(nums5, nums6));
        int[] nums7 = new int[]{}, nums8 = new int[]{1};
        System.out.println(findMedianSortedArrays(nums7, nums8));
        int[] nums9 = new int[]{2}, nums10 = new int[]{};
        System.out.println(findMedianSortedArrays(nums9, nums10));
        int[] nums11 = new int[]{0,0,0,0,0}, nums12 = new int[]{-1,0,0,0,0,0,1};
        System.out.println(findMedianSortedArrays(nums11, nums12));
    }
}
```

打印：

```
[1, 2, 3]
2.0
[1, 2, 3, 4]
2.5
[0, 0, 0, 0]
0.0
1.0
2.0
[-1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1]
0.0

Process finished with exit code 0
```

思路：

- 就是将两个数组合成一个数组，但是考虑几种情况，然后做处理。