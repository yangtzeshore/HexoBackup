---
title: NextPermutation
date: 2021-02-05 09:22:21
tags:
- 
categories:
- Leetcode 
---



## 下一个排列

- [题目介绍](https://yangtzeshore.github.io/2021/02/05/NextPermutation/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/02/05/NextPermutation/#题目解法)

### 题目介绍

#### [下一个排列](https://leetcode-cn.com/problems/next-permutation/)

实现获取 **下一个排列** 的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。

如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。

必须**[ 原地 ](https://baike.baidu.com/item/原地算法)**修改，只允许使用额外常数空间。

**示例 1：**

```
输入：nums = [1,2,3]
输出：[1,3,2]
```

**示例 2：**

```
输入：nums = [3,2,1]
输出：[1,2,3]
```

**示例 3：**

```
输入：nums = [1,1,5]
输出：[1,5,1]
```

**示例 4：**

```
输入：nums = [1]
输出：[1]
```

### 题目解法

```
package algorithm;

public class NextPermutation {

    public static void nextPermutation(int[] nums) {

        int i = nums.length - 2;
        while (i >= 0 && nums[i] >= nums[i + 1]) {
            i--;
        }
        if (i >= 0) {
            int j = nums.length - 1;
            while (j > i && nums[i] >= nums[j]) {
                j--;
            }
            swap(nums, i, j);
        }
        reverse(nums, i + 1);
    }

    private static void reverse(int[] nums, int left) {
        int right = nums.length - 1;
        while (left < right) {
            swap(nums, left, right);
            left ++;
            right --;
        }
    }

    private static void swap(int[] nums, int left, int right) {
        int temp = nums[left];
        nums[left] = nums[right];
        nums[right] = temp;
    }

    public static void main(String[] args) {

        // nums = [1,2,3]
        int[] nums = new int[]{1, 2, 3};
        nextPermutation(nums);
        print(nums);

        nums = new int[]{3, 2, 1};
        nextPermutation(nums);
        print(nums);

        nums = new int[]{1, 1, 5};
        nextPermutation(nums);
        print(nums);

        nums = new int[]{1};
        nextPermutation(nums);
        print(nums);

    }

    private static void print(int[] nums) {
        for (int num : nums) {
            System.out.print(num);
        }
        System.out.println();
    }
}
```

打印：

```
132
123
151
1
```

**思路：**

这道题没做出来。主要是需要证明一个算法有效性：就是从右往左扫描，分两次。第一次，需要判定某个数往右，是降序排列；然后，交换这个数字与右边从后往前第一个大于它的数字，然后将右边数字升序。这种算法是容易证明是正确的，但是需要意识到这个问题。