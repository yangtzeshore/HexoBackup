---
title: ImplementStrstr
date: 2021-01-31 09:22:21
tags:
- 
categories:
- Leetcode 
---



## 实现 strStr()

- [题目介绍](https://yangtzeshore.github.io/2021/01/31/ImplementStrstr/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/01/31/ImplementStrstr/#题目解法)

### 题目介绍

[实现 strStr()](https://leetcode-cn.com/problems/implement-strstr/)

实现 [strStr()](https://baike.baidu.com/item/strstr/811469) 函数。

给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回 **-1**。

**示例 1：**

```
输入: haystack = "hello", needle = "ll"
输出: 2
```

**示例 2：**

```
输入: haystack = "aaaaa", needle = "bba"
输出: -1
```

**说明:**

当 `needle` 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。

对于本题而言，当 `needle` 是空字符串时我们应当返回 0 。这与C语言的 [strstr()](https://baike.baidu.com/item/strstr/811469) 以及 Java的 [indexOf()](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html#indexOf(java.lang.String)) 定义相符。

### 题目解法

```
package algorithm;

public class ImplementStrstr {

    public static int strStr(String haystack, String needle) {

        if (needle == null || needle.length() == 0) {
            return 0;
        }

        int hayLength = haystack.length();
        int needleLength = needle.length();
        for (int i = 0; i <= hayLength - needleLength; i++) {
            if (haystack.substring(i, i + needleLength).equals(needle)) {
                return i;
            }
        }
        return -1;
    }

    public static void main(String[] args) {
        String haystack = "hello";
        String needle = "ll";
        System.out.println(strStr(haystack, needle));

        haystack = "aaaaa";
        needle = "bba";
        System.out.println(strStr(haystack, needle));
    }
}
```

打印：

```
2
-1
```

**思路：**

思路很简单，就是依次匹配。但是这里有个问题，我第一次用char挨个匹配，但是超时了；改用substring才能不超时。其二，官方的第二种算法，我也想到过，这里就不展示了，不过效率确实很好。