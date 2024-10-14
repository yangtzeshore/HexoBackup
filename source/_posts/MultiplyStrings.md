---
title: MultiplyStrings
date: 2021-03-02 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 字符串相乘

- [题目介绍](https://yangtzeshore.github.io/2021/03/02/MultiplyStrings/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/03/02/MultiplyStrings/#题目解法)

### 题目介绍

#### [字符串相乘](https://leetcode-cn.com/problems/multiply-strings/)

给定两个以字符串形式表示的非负整数 `num1` 和 `num2`，返回 `num1` 和 `num2` 的乘积，它们的乘积也表示为字符串形式。

**示例 1：**

```
输入: num1 = "2", num2 = "3"
输出: "6"
```

**示例 2：**

```
输入: num1 = "123", num2 = "456"
输出: "56088"
```

**提示：**

- `num1 和 num2 的长度小于110。`
- `num1 和 num2 只包含数字 0-9。`
- `num1 和 num2 均不以零开头，除非是数字 0 本身。`
- **不能使用任何标准库的大数类型（比如 BigInteger）**或**直接将输入转换为整数来处理**。

### 题目解法

```
package algorithm;

import java.util.Arrays;

public class MultiplyStrings {

    public String multiply1(String num1, String num2) {
        if (num1.equals("0") || num2.equals("0")) {
            return "0";
        }
        int m = num1.length(), n = num2.length();
        int[] ansArr = new int[m + n];
        for (int i = m - 1; i >= 0; i--) {
            int x = num1.charAt(i) - '0';
            for (int j = n - 1; j >= 0; j--) {
                int y = num2.charAt(j) - '0';
                ansArr[i + j + 1] += x * y;
            }
        }
        for (int i = m + n - 1; i > 0; i--) {
            ansArr[i - 1] += ansArr[i] / 10;
            ansArr[i] %= 10;
        }
        int index = ansArr[0] == 0 ? 1 : 0;
        StringBuffer ans = new StringBuffer();
        while (index < m + n) {
            ans.append(ansArr[index]);
            index++;
        }
        return ans.toString();
    }

    public static String multiply(String num1, String num2) {
        if (num1.equals("0") || num2.equals("0")) {
            return "0";
        }

        int[] result = new int[222];
        Arrays.fill(result, 0);

        char[] chars1 = num1.toCharArray();
        char[] chars2 = num2.toCharArray();

        int length1 = chars1.length - 1;//456
        for (int i = length1; i >= 0; i--) { //123
            int length2 = chars2.length - 1;

            int carry = 0;
            int index = 0;
            for (int j = length2; j >= 0; j--) {//456
                int temp = (chars1[i] - '0') * (chars2[j] - '0');
                index = length1 - i + length2 - j;
                temp += result[index] + carry;

                result[index] = temp % 10;
                carry = temp / 10;
            }
            if (carry > 0) {
               result[index + 1] = carry;
            }
        }
        StringBuilder sb = new StringBuilder();
        boolean first = false;
        for (int i = result.length - 1; i >= 0; i--) {
            if (!first && result[i] != 0) {
                sb.append(result[i]);
                first = true;
            } else if (first) {
                sb.append(result[i]);
            }
        }
        return sb.toString();
    }

    public static void main(String[] args) {
        System.out.println(multiply("2", "3"));

        System.out.println(multiply("123", "456"));

        System.out.println(multiply("999", "99999"));
    }
}
```

打印：

```
6
56088
99899001
```

**思路：**

思路上，自己写了一版，效率一般，类似于官方的乘法算法，比加法算法效率好一点。官方的乘法优化非常彻底，可以仔细品味。