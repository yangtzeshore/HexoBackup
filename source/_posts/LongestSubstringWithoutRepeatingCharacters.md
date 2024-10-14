---
title: LongestSubstringWithoutRepeatingCharacters
date: 2020-12-29 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 题目介绍

[无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

示例 1:

```
输入: s = "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

示例 2:

```
输入: s = "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```

示例 3:

```
输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```

示例 4:

```
输入: s = ""
输出: 0
```

## 题目解法

```
package algorithm;

public class LongestSubstringWithoutRepeatingCharacters {

    public static int lengthOfLongestSubstring(String s) {
        // 记录字符上一次出现的位置
        int[] last = new int[128];
        for(int i = 0; i < 128; i++) {
            last[i] = -1;
        }
        int n = s.length();

        int result = 0;
        int start = 0; // 窗口开始位置
        for(int i = 0; i < n; i++) {
            // 字符转数字
            int index = s.charAt(i);
            // 最主要的就是这一行，找到起始位置：分两种情况：
            // 一直都是不同的字符，那start不变
            // 遇到重复的，也分两种情况，一种是和起始重复，从重复的字符+1位
            // 一种是和后面的字符重复，也是从重复的字符+1位
            start = Math.max(start, last[index] + 1);
            // 记录最长串长度
            result = Math.max(result, i - start + 1);
            // 记录最新的该字符所在位置
            last[index] = i;
        }

        return result;
    }

    public static void main(String[] args) {
        System.out.println(lengthOfLongestSubstring("abcabcbb"));
        System.out.println(lengthOfLongestSubstring("bbbbb"));
        System.out.println(lengthOfLongestSubstring("cd"));
    }
}
```

打印：

```
3
1
2
```

思路：

- 找到起始位置：分两种情况：一直都是不同的字符，那start不变；遇到重复的，也分两种情况，一种是和起始重复，从起始重复的字符+1位，一种是和后面的字符重复，也是从后面的字符重复的字符+1位。