---
title: LongestValidParentheses
date: 2021-02-06 09:22:21
tags:
- 
categories:
- Leetcode 
---



## 最长有效括号

- [题目介绍](https://yangtzeshore.github.io/2021/02/06/LongestValidParentheses/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/02/06/LongestValidParentheses/#题目解法)

### 题目介绍

#### [最长有效括号](https://leetcode-cn.com/problems/longest-valid-parentheses/)

给你一个只包含 `'('` 和 `')'` 的字符串，找出最长有效（格式正确且连续）括号子串的长度。

**示例 1：**

```
输入：s = "(()"
输出：2
解释：最长有效括号子串是 "()"
```

**示例 2：**

```
输入：s = ")()())"
输出：4
解释：最长有效括号子串是 "()()"
```

**示例 3：**

```
输入：s = ""
输出：0xxxxxxxxxx 输入：s = ""输出：0输入：nums = [1,1,5]输出：[1,5,1]
```

### 题目解法

```
package algorithm;

public class LongestValidParentheses {

    public static int longestValidParentheses(String s) {
        int[] dp = new int[s.length()];

        int max = 0;
        for (int i = 1; i < s.length(); i++) {
            if (s.charAt(i) == ')') {
                if (s.charAt(i - 1) == '(') {
                    dp[i] = (i > 2 ? dp[i - 2] : 0) + 2;
                } else if (i - dp[i - 1] > 0 && s.charAt(i - dp[i - 1] - 1) == '(') {
                    dp[i] = dp[i - 1] + (i - dp[i - 1] >= 2 ? dp[i - dp[i - 1] - 2] : 0) + 2;
                }
                max = Math.max(max, dp[i]);
            }
        }
        print(dp);

        return max;
    }

    private static void print(int[] dp) {
        for (int i : dp) {
            System.out.print(i);
        }
        System.out.println();
    }

    public static void main(String[] args) {

        String s = "(()";
        System.out.println(longestValidParentheses(s));
        s = ")()())";
        System.out.println(longestValidParentheses(s));
        s = "";
        System.out.println(longestValidParentheses(s));
    }
}
```

打印：

```
002
2
002040
4

0
```

**思路：**

这道题思路很简单，就是dp，写出方程后，就简单了。不过，需要注意，只是求最大值，不是求每个dp位置上准确的数值。