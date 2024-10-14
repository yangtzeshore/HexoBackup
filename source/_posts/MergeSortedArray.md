---
title: MergeSortedArray
date: 2021-06-15 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 扰乱字符串

- [题目介绍](https://yangtzeshore.github.io/2021/06/15/MergeSortedArray/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/06/15/MergeSortedArray/#题目解法)

### 题目介绍

#### [合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/)

给你两个有序整数数组 `nums1` 和 `nums2`，请你将 `nums2` 合并到 `nums1` 中*，*使 `nums1` 成为一个有序数组。

初始化 `nums1` 和 `nums2` 的元素数量分别为 `m` 和 `n` 。你可以假设 `nums1`的空间大小等于 `m + n`，这样它就有足够的空间保存来自 `nums2` 的元素。

**示例1：**

```
输入：nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
输出：[1,2,2,3,5,6]
```

**示例 2：**

```
输入：nums1 = [1], m = 1, nums2 = [], n = 0
输出：[1]
```

**提示：**

- `nums1.length == m + n`
- `nums2.length == n`
- `0 <= m, n <= 200`
- `1 <= m + n <= 200`
- `-109 <= nums1[i], nums2[i] <= 109`

### 题目解法

```
package algorithm;

import java.util.Arrays;

public class MergeSortedArray {

    public static void merge(int[] nums1, int m, int[] nums2, int n) {
        System.arraycopy(nums2, 0, nums1, m, n);
        Arrays.sort(nums1);
    }

    public static void main(String[] args) {

        int[] nums1 = new int[]{1,2,3,0,0,0};
        int[] nums2 = new int[]{2,5,6};
        merge(nums1, 3, nums2, 3);
        System.out.println(Arrays.toString(nums1));

        nums1 = new int[]{1};
        nums2 = new int[]{};
        merge(nums1, 1, nums2, 0);
        System.out.println(Arrays.toString(nums1));
    }
}
```

打印：

```
[1, 2, 2, 3, 5, 6]
[1]
```

**思路：**

思路上，我偷懒了。逆双指针不是没想到，只是懒了，警戒下。需要好好对待每道题。