---
title: LongestPalindrome
date: 2021-01-02 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 题目介绍

[最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

给定一个字符串 `s`，找到 `s` 中最长的回文子串。你可以假设 `s` 的最大长度为 1000。

示例 1:

```
输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
```

示例 2:

```
输入: "cbbd"
输出: "bb"
```

## 题目解法

1、下面第一版自己写的，比较粗糙

```
package algorithm;

public class LongestPalindrome {

    public static String longestPalindrome(String s) {

        if (s.length() <= 1) {
            return s;
        }
        int start = 0, end = 0, maxLength = 0;
        for(int i = 0; i < s.length() - 1; i ++) {
            for (int j = s.length() - 1; j > i; j --) {
                if (checkPalindrome(s.substring(i, j + 1))) {
                    if (j - i + 1 > maxLength) {
                        start = i;
                        end = j;
                        maxLength = j - i + 1;
                    }
                }
                if (j - i + 1 <= maxLength) {
                    break;
                }
            }
            if (s.length() - i <= maxLength) {
                break;
            }
        }

        return s.substring(start, end + 1);
    }

    public static boolean checkPalindrome(String s) {
        for (int i = 0; i < s.length() / 2; i++) {
            if (s.charAt(i) != s.charAt(s.length() - i - 1)) {
                return false;
            }
        }
        return true;
    }

    public static void main(String[] args) {
        String s = "bbabba";
        System.out.println(longestPalindrome(s));
        s = "cbbd";
        System.out.println(longestPalindrome(s));
        s = "bb";
        System.out.println(longestPalindrome(s));
    }
}
```

打印：

```
bbabb
bb
bb
```

思路：

- 就是多次循环判断找回文。

2、第一版耗时太久，下面第二版就优化了一下

```
package algorithm;

public class LongestPalindrome {

    public static String longestPalindrome(String s) {

        if (s.length() <= 1) {
            return s;
        }
        int start = 0, index = 0, maxLength = 0;

        while (index < s.length()) {
            int left = index, right = index;
            // 先找到字符一样的字符串
            while (index < s.length() - 1 && s.charAt(index) == s.charAt(index + 1)) {
                index ++;
                right ++;
            }
            // 从某个点开始，向两边扩散寻找最长串
            while (left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
                left --;
                right ++;
            }
            left ++;
            
            if (right - left + 1 > maxLength) {
                maxLength = right - left +1;right --;
                start = left;
            }
            index ++;
        }

        return s.substring(start, start + maxLength);
    }

    public static void main(String[] args) {
        String s = "bbabba";
        System.out.println(longestPalindrome(s));
        s = "cbbd";
        System.out.println(longestPalindrome(s));
        s = "bb";
        System.out.println(longestPalindrome(s));
    }
}
```

打印：

```
bbabb
bb
bb
```

思路：

- 就是分两种情况，第一种，找最长相同字符串，第二种，就是从某个字符开始，向两边扩散，找回文。当然这两种是可以放在一起的，如代码。这样就解决了双重循环找回文的次数，耗时当然少了。