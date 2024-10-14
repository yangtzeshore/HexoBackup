---
title: RemoveDuplicatesFromSortedArrayII
date: 2021-05-25 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 删除有序数组中的重复项 II

- [题目介绍](https://yangtzeshore.github.io/2021/05/25/RemoveDuplicatesFromSortedArrayII/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/05/25/RemoveDuplicatesFromSortedArrayII/#题目解法)

### 题目介绍

#### [删除有序数组中的重复项 II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array-ii/)

给你一个有序数组 `nums` ，请你**[ 原地](http://baike.baidu.com/item/原地算法)** 删除重复出现的元素，使每个元素 **最多出现两次** ，返回删除后数组的新长度。

不要使用额外的数组空间，你必须在 **[原地 ](https://baike.baidu.com/item/原地算法)修改输入数组** 并在使用 O(1) 额外空间的条件下完成。

**说明：**

为什么返回数值是整数，但输出的答案是数组呢？

请注意，输入数组是以**「引用」**方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。

你可以想象内部操作如下:

```
// nums 是以“引用”方式传递的。也就是说，不对实参做任何拷贝
int len = removeDuplicates(nums);

// 在函数里修改输入数组对于调用者是可见的。
// 根据你的函数返回的长度, 它会打印出数组中 该长度范围内 的所有元素。
for (int i = 0; i < len; i++) {
    print(nums[i]);
}
```

**示例1：**

```
输入：nums = [1,1,1,2,2,3]
输出：5, nums = [1,1,2,2,3]
解释：函数应返回新长度 length = 5, 并且原数组的前五个元素被修改为 1, 1, 2, 2, 3 。 不需要考虑数组中超出新长度后面的元素。
```

**示例 2：**

```
输入：nums = [0,0,1,1,1,1,2,3,3]
输出：7, nums = [0,0,1,1,2,3,3]
解释：函数应返回新长度 length = 7, 并且原数组的前五个元素被修改为 0, 0, 1, 1, 2, 3, 3 。 不需要考虑数组中超出新长度后面的元素。
```

**提示：**

- 1<=nums.length<=3∗104
- −104<=nums[i]<=104
- `nums` 已按升序排列

### 题目解法

```
package algorithm;

public class RemoveDuplicatesFromSortedArrayII {

    public static int removeDuplicates(int[] nums) {

        int length = nums.length;
        if (length <= 2) {
            return length;
        }

        int slow = 2;
        int fast = 2;
        while (fast < length) {
            if (nums[slow - 2] != nums[fast]) {
                nums[slow] = nums[fast];
                slow ++;
            }
            fast++;
        }

        return slow;
    }

    public static void main(String[] args) {

        int[] nums = new int[]{1,1,1,2,2,3};
        System.out.println(removeDuplicates(nums));
        print(nums);

        nums = new int[]{0,0,1,1,1,1,2,3,3};
        System.out.println(removeDuplicates(nums));
        print(nums);

    }

    private static void print(int[] nums) {
        for (int num : nums) {
            System.out.print(num + " ");
        }
        System.out.println();
    }
}
```

打印：

```
5
1 1 2 2 3 3 
7
0 0 1 1 2 3 3 3 3 
```

**思路：**

思路很简单。就是双指针，然后复制交换。