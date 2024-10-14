---
title: InterleavingString
date: 2021-07-03 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 交错字符串

- [题目介绍](https://yangtzeshore.github.io/2021/07/03/InterleavingString/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/07/03/InterleavingString/#题目解法)

### 题目介绍

#### [交错字符串](https://leetcode-cn.com/problems/interleaving-string/)

给定三个字符串 `s1`、`s2`、`s3`，请你帮忙验证 `s3` 是否是由 `s1` 和 `s2` **交错** 组成的。

两个字符串 `s` 和 `t` **交错** 的定义与过程如下，其中每个字符串都会被分割成若干 **非空** 子字符串：

- `s = s1 + s2 + ... + sn`
- `t = t1 + t2 + ... + tm`
- `|n - m| <= 1`
- **交错** 是 `s1 + t1 + s2 + t2 + s3 + t3 + ...` 或者 `t1 + s1 + t2 + s2 + t3 + s3 + ...`

**提示：**`a + b` 意味着字符串 `a` 和 `b` 连接。

**示例 1：**

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/interleave.jpg)

```
输入：s1 = "aabcc", s2 = "dbbca", s3 = "aadbbcbcac"
输出：true
```

**示例 2：**

```
输入：s1 = "aabcc", s2 = "dbbca", s3 = "aadbbbaccc"
输出：falsexxxxxxxxxx 输入：s1 = "aabcc", s2 = "dbbca", s3 = "aadbbbaccc"输出：false输入：n = 1输出：1
```

**示例 3：**

```
输入：s1 = "", s2 = "", s3 = ""
输出：true
```

**提示：**

- `0 <= s1.length, s2.length <= 100`
- `0 <= s3.length <= 200`
- `s1`、`s2`、和 `s3` 都由小写英文字母组成

### 题目解法

```
package algorithm;

public class InterleavingString {

    public static boolean isInterleave(String s1, String s2, String s3) {

        int n = s1.length();
        int m = s2.length();
        int t = s3.length();

        if (n + m != t) {
            return false;
        }

        boolean[][] f = new boolean[n + 1][m + 1];
        f[0][0] = true;

        // 注意索引从0开始
        for (int i = 0; i <= n; i++) {
            for (int j = 0; j <= m; j++) {
                int p = i + j - 1;
                // 主要是i和j可能都大于1，这是需要或
                if (i > 0) {
                    f[i][j] = f[i][j] || (f[i - 1][j] && s1.charAt(i - 1) == s3.charAt(p));
                }
                if (j > 0) {
                    f[i][j] = f[i][j] || (f[i][j - 1] && s2.charAt(j - 1) == s3.charAt(p));
                }
            }
        }

        return f[n][m];
    }

    public static void main(String[] args) {
        String s1 = "aabcc";
        String s2 = "dbbca";
        String s3 = "aadbbcbcac";
        System.out.println(isInterleave(s1, s2, s3));

        s3 = "aadbbbaccc";
        System.out.println(isInterleave(s1, s2, s3));

        s1 = "";
        s2 = "";
        s3 = "";
        System.out.println(isInterleave(s1, s2, s3));
    }
}
```

打印：

```
true
false
true
```

**思路：**

思路上，动态规划。主要是要注意边界为0的情况。