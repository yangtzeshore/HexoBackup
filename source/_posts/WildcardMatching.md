---
title: WildcardMatching
date: 2021-03-04 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 通配符匹配

- [题目介绍](https://yangtzeshore.github.io/2021/03/04/WildcardMatching/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/03/04/WildcardMatching/#题目解法)

### 题目介绍

#### [通配符匹配](https://leetcode-cn.com/problems/wildcard-matching/)

给定一个字符串 (`s`) 和一个字符模式 (`p`) ，实现一个支持 `'?'` 和 `'*'` 的通配符匹配。

```
'?' 可以匹配任何单个字符。
'*' 可以匹配任意字符串（包括空字符串）。
```

两个字符串**完全匹配**才算匹配成功。

**说明:**

- `s` 可能为空，且只包含从 `a-z` 的小写字母。
- `p` 可能为空，且只包含从 `a-z` 的小写字母，以及字符 `?` 和 `*`。

**示例 1：**

```
输入:
s = "aa"
p = "a"
输出: false
解释: "a" 无法匹配 "aa" 整个字符串。
```

**示例 2：**

```
输入:
s = "aa"
p = "*"
输出: true
解释: '*' 可以匹配任意字符串。
```

**示例 3：**

```
输入:
s = "cb"
p = "?a"
输出: false
解释: '?' 可以匹配 'c', 但第二个 'a' 无法匹配 'b'。
```

**示例 4：**

```
输入:
s = "adceb"
p = "*a*b"
输出: true
解释: 第一个 '*' 可以匹配空字符串, 第二个 '*' 可以匹配字符串 "dce".
```

**示例 5：**

```
输入:
s = "acdcb"
p = "a*c?b"
输出: false
```

### 题目解法

```
package algorithm;

public class WildcardMatching {

    public static boolean isMatch(String s, String p) {
        int m = s.length();
        int n = p.length();
        boolean[][] dp = new boolean[m + 1][n + 1];
        dp[0][0] = true;

        for (int i = 1; i <= n; i++) {
            if (p.charAt(i - 1) == '*') {
                dp[0][i] = true;
            } else {
                break;
            }
        }
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (p.charAt(j - 1) == '*') {
                    dp[i][j] = dp[i][j - 1] || dp[i - 1][j];
                } else if (p.charAt(j - 1) == '?' || s.charAt(i - 1) == p.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                }
            }
        }
        return dp[m][n];
    }

    public static void main(String[] args) {
        System.out.println(isMatch("aa", "a"));

        System.out.println(isMatch("aa", "*"));//t

        System.out.println(isMatch("cb", "?a"));

        System.out.println(isMatch("adceb", "*a*b"));//t

        System.out.println(isMatch("acdcb", "a*c?b"));
    }
}
```

打印：

```
false
true
false
true
false
```

**思路：**

思路上，自己写了一版，效率一般，用的是递归。所以改用了官方的动态方程，其实效率还是一般。动态方程和边界条件写好了，答案也就出来了，不是很难。