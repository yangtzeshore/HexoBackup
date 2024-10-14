---
title: PlusOne
date: 2021-04-25 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 加一

- [题目介绍](https://yangtzeshore.github.io/2021/04/25/PlusOne/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/04/25/PlusOne/#题目解法)

### 题目介绍

#### [加一](https://leetcode-cn.com/problems/plus-one/)

给定一个由 **整数** 组成的 **非空** 数组所表示的非负整数，在该数的基础上加一。

最高位数字存放在数组的首位， 数组中每个元素只存储**单个**数字。

你可以假设除了整数 0 之外，这个整数不会以零开头。

**示例1：**

```
输入：digits = [1,2,3]
输出：[1,2,4]
解释：输入数组表示数字 123。
```

**示例2：**

```
输入：digits = [4,3,2,1]
输出：[4,3,2,2]
解释：输入数组表示数字 4321。
```

**示例 3：**

```
输入：digits = [0]
输出：[1]
```

**提示：**

- `1 <= digits.length <= 100`
- `0 <= digits[i] <= 9`

### 题目解法

```
package algorithm;

public class PlusOne {

    public static int[] plusOne(int[] digits) {

        int length = digits.length;
        int step = 1;
        for (int i = length - 1; i >= 0; i--) {
            int temp = digits[i];
            digits[i] = (temp + step) % 10;
            step = (temp + step) / 10;
        }

        if (step != 0) {
            int[] digitss = new int[length + 1];
            digitss[0] = step;
            for (int i = 0; i < length; i++) {
                digitss[i + 1] = digits[i];
            }
            return digitss;
        }

        return digits;
    }

    public static void main(String[] args) {
        int[] digits = new int[]{1,2,3} ;
        print(plusOne(digits));

        digits = new int[]{4,3,2,1} ;
        print(plusOne(digits));

        digits = new int[]{0} ;
        print(plusOne(digits));

        digits = new int[]{9} ;
        print(plusOne(digits));
    }

    private static void print(int[] digits) {
        for (int digit : digits) {
            System.out.print(digit);
            System.out.print(" ");
        }
        System.out.println();
    }
}
```

打印：

```
1 2 4 
4 3 2 2 
1 
1 0 
```

**思路：**

简单。