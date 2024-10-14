---
title: AdditiveNumber
date: 2024-10-12 09:58:01
tags:
- 
categories:
- Leetcode 

---



## AdditiveNumber

### 题目介绍

[306. 累加数](https://leetcode.cn/problems/additive-number/description/)

**累加数** 是一个字符串，组成它的数字可以形成累加序列。

一个有效的 **累加序列** 必须 **至少** 包含 3 个数。除了最开始的两个数以外，序列中的每个后续数字必须是它之前两个数字之和。

给你一个只包含数字 `'0'-'9'` 的字符串，编写一个算法来判断给定输入是否是 **累加数** 。如果是，返回 `true` ；否则，返回 `false` 。

**说明：**累加序列里的数，除数字 0 之外，**不会** 以 0 开头，所以不会出现 `1, 2, 03` 或者 `1, 02, 3` 的情况。

 

**示例 1：**

```
输入："112358"
输出：true 
解释：累加序列为: 1, 1, 2, 3, 5, 8 。1 + 1 = 2, 1 + 2 = 3, 2 + 3 = 5, 3 + 5 = 8
```

**示例 2：**

```
输入："199100199"
输出：true 
解释：累加序列为: 1, 99, 100, 199。1 + 99 = 100, 99 + 100 = 199
```

 

**提示：**

- `1 <= num.length <= 35`
- `num` 仅由数字（`0` - `9`）组成

 

**进阶：**你计划如何处理由过大的整数输入导致的溢出?



### 题目解法

```
public class AdditiveNumber {

    public boolean isAdditiveNumber(String num) {

        int length = num.length();

        for (int secondStart = 1; secondStart < length - 1; secondStart++) {
            if (num.charAt(0) == '0' && secondStart != 1) {
                break;
            }
            for (int secondEnd = secondStart; secondEnd < length - 1; secondEnd++) {
                if (num.charAt(secondStart) == '0' && secondStart != secondEnd) {
                    break;
                }
                if (valid(num, secondStart, secondEnd)) {
                    return true;
                }
            }
        }
        return false;
    }

    public boolean valid(String num, int secondStart, int secondEnd) {
        int firstStart = 0;
        while (secondEnd <= num.length() - 1) {
            String thirdNum = stringAdd(num, firstStart, secondStart, secondEnd);
            int thirdStart = secondEnd + 1;
            int thirdEnd = secondEnd + thirdNum.length();
            if (thirdEnd >= num.length() || !num.substring(thirdStart, thirdEnd + 1).equals(thirdNum)) {
                break;
            }
            if (thirdEnd == num.length() - 1) {
                return true;
            }
            firstStart = secondStart;
            secondStart = secondEnd + 1;
            secondEnd = secondEnd + thirdNum.length();

        }
        return false;
    }

    private String stringAdd(String num, int firstStart, int secondStart, int secondEnd) {
        int carry = 0, cur = 0;
        int firstEnd = secondStart - 1;
        StringBuilder sb = new StringBuilder();
        while (firstStart <= firstEnd || secondStart <= secondEnd || carry != 0) {
            cur = carry;
            if (firstEnd >= firstStart) {
                cur += num.charAt(firstEnd) - '0';
            }
            if (secondEnd >= secondStart) {
                cur += num.charAt(secondEnd) - '0';
            }
            carry = cur / 10;
            cur = cur % 10;
            sb.append((char)(cur + '0'));
            firstEnd--;
            secondEnd--;
        }
        sb.reverse();
        return sb.toString();
    }



}
```

打印：

```

```



### 思路

其实就是暴力解决，利用中间第二个数字的起始位置，作为最重要的分界点。

​	