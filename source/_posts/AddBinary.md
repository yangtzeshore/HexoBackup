---
title: AddBinary
date: 2021-04-27 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 二进制求和

- [题目介绍](https://yangtzeshore.github.io/2021/04/27/AddBinary/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/04/27/AddBinary/#题目解法)

### 题目介绍

#### [二进制求和](https://leetcode-cn.com/problems/add-binary/)

给你两个二进制字符串，返回它们的和（用二进制表示）。

输入为 **非空** 字符串且只包含数字 `1` 和 `0`。

**示例1：**

```
输入: a = "11", b = "1"
输出: "100"
```

**示例2：**

```
输入: a = "1010", b = "1011"
输出: "10101"
```

**提示：**

- 每个字符串仅由字符 `'0'` 或 `'1'` 组成。
- `1 <= a.length, b.length <= 10^4`
- 字符串如果不是 `"0"` ，就都不含前导零。

### 题目解法

```
package algorithm;

public class AddBinary {

    public static String addBinary(String a, String b) {

        char[] charsA = a.toCharArray();
        char[] charsB = b.toCharArray();
        int step = 0;
        int m = a.length();
        int n = b.length();
        StringBuilder sb = new StringBuilder();
        int max = Math.max(m, n);
        for (int i = 1; i <= max; i++) {
            int sum = 0;
            if (m - i >= 0) {
                sum += charsA[m - i] - '0';
            }
            if (n - i >= 0) {
                sum += charsB[n - i] - '0';
            }
            sum += step;
            StringBuilder temp = new StringBuilder().append(sum % 2);
            sb = temp.append(sb);
            step = sum / 2;
        }
        if (step == 1) {
            return "1" + sb;
        }

        return sb.toString();
    }

    public static void main(String[] args) {
        System.out.println(addBinary("11", "1"));

        System.out.println(addBinary("1010", "1011"));
    }
}
```

打印：

```
100
10101
```

**思路：**

简单。