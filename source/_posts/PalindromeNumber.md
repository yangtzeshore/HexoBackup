---
title: PalindromeNumber
date: 2021-01-09 09:22:21
tags:
- 
categories:
- Leetcode 
---



## 回文数

- [题目介绍](https://yangtzeshore.github.io/2021/01/09/PalindromeNumber/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/01/09/PalindromeNumber/#题目解法)

### 题目介绍

[回文数](https://leetcode-cn.com/problems/palindrome-number/)

判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

**示例 1:**

```
输入: 121
输出: true
```

**示例 2:**

```
输入: -121
输出: false
解释: 从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
```

**示例 3:**

```
输入: 10
输出: false
解释: 从右向左读, 为 01 。因此它不是一个回文数。
进阶:
```

**进阶:**

你能不将整数转为字符串来解决这个问题吗？

## 题目解法

这道题如果不考虑进阶，那很简单，下面是自己写的一版。

```
package algorithm;

public class PalindromeNumber {

    public static boolean isPalindrome(int x) {
        String result = String.valueOf(x);
        if (result.length() == 1) {
            return true;
        }

        for (int i = 0; i < result.length() / 2; i++) {
            if (result.charAt(i) != result.charAt(result.length() - i - 1)) {
                return false;
            }
        }

        return true;
    }

    public static void main(String[] args) {
        System.out.println(isPalindrome(121));
        System.out.println(isPalindrome(-121));
        System.out.println(isPalindrome(10));
    }
}
```

打印：

```
true
false
false
```

**思路：**

就是类似于暴力破解。

如果是进阶版，不需要用字符串：

```
public static boolean isPalindrome1(int x) {
        if (x < 0) {
            return false;
        }
        int result = 0;
        int temp = x;
        while(temp > 0) {
            int modNum = temp % 10;
            result = result * 10 + modNum;
            temp /= 10;
        }
        return x == result;
    }
```

**思路：**

也很简单，无法是取模。