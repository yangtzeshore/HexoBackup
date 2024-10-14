---
title: MinimumWindowSubstring
date: 2021-05-13 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 颜色分类

- [题目介绍](https://yangtzeshore.github.io/2021/05/13/SortColors/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/05/13/SortColors/#题目解法)

### 题目介绍

#### [颜色分类](https://leetcode-cn.com/problems/sort-colors/)

给定一个包含红色、白色和蓝色，一共 `n` 个元素的数组，**[原地](https://baike.baidu.com/item/原地算法)**对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

此题中，我们使用整数 `0`、 `1` 和 `2` 分别表示红色、白色和蓝色。

**示例1：**

```
输入：nums = [2,0,2,1,1,0]
输出：[0,0,1,1,2,2]
```

**示例2：**

```
输入：nums = [2,0,1]
输出：[0,1,2]
```

**示例3：**

```
输入：nums = [0]
输出：[0]
```

**示例4：**

```
输入：nums = [1]
输出：[1]
```

**提示：**

- `n == nums.length`
- `1 <= n <= 300`
- `nums[i]` 为 `0`、`1` 或 `2`

**进阶：**

- 你可以不使用代码库中的排序函数来解决这道题吗？
- 你能想出一个仅使用常数空间的一趟扫描算法吗？

### 题目解法

```
package algorithm;

import java.util.Arrays;

public class SortColors {

    public static void sortColors(int[] nums) {
        if (nums.length <= 1) {
            return;
        }

        int red = 0;
        int white = 0;
        int blue = 0;
        for (int num : nums) {
            if (num == 0) {
                red++;
            } else if (num == 1) {
                white++;
            } else {
                blue++;
            }
        }
        Arrays.fill(nums, 0, red, 0);
        Arrays.fill(nums, red, red + white, 1);
        Arrays.fill(nums, red + white, red + white + blue, 2);
    }

    public static void sortColors1(int[] nums) {
        int n = nums.length;
        int p0 = 0, p1 = 0;
        for (int i = 0; i < n; ++i) {
            if (nums[i] == 1) {
                int temp = nums[i];
                nums[i] = nums[p1];
                nums[p1] = temp;
                ++p1;
            } else if (nums[i] == 0) {
                int temp = nums[i];
                nums[i] = nums[p0];
                nums[p0] = temp;
                if (p0 < p1) {
                    temp = nums[i];
                    nums[i] = nums[p1];
                    nums[p1] = temp;
                }
                ++p0;
                ++p1;
            }
        }
    }

    public static void sortColors2(int[] nums) {
        int n = nums.length;
        int p0 = 0, p2 = n - 1;
        for (int i = 0; i <= p2; ++i) {
            while (i <= p2 && nums[i] == 2) {
                int temp = nums[i];
                nums[i] = nums[p2];
                nums[p2] = temp;
                --p2;
            }
            if (nums[i] == 0) {
                int temp = nums[i];
                nums[i] = nums[p0];
                nums[p0] = temp;
                ++p0;
            }
        }
    }

    public static void main(String[] args) {
        int[] nums = new int[] {2,0,2,1,1,0};
        sortColors(nums);
        print(nums);

        nums = new int[] {2,0,1};
        sortColors(nums);
        print(nums);

        nums = new int[] {0};
        sortColors(nums);
        print(nums);

        nums = new int[] {1};
        sortColors(nums);
        print(nums);
    }

    private static void print(int[] nums) {
        for (int num : nums) {
            System.out.print(num);
            System.out.print(" ");
        }
        System.out.println();
    }
}
```

打印：

```
0 0 1 1 2 2 
0 1 2 
0 
1 
```

**思路：**

思路统计并填充法很简单，但是双指针也很巧妙。