---
title: DistinctSubsequences
date: 2021-08-01 09:22:21
tags:
- 
categories:
- Leetcode 
---



### 不同的子序列

- [题目介绍](https://yangtzeshore.github.io/2021/08/01/DistinctSubsequences/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/08/01/DistinctSubsequences/#题目解法)

### 题目介绍

#### [不同的子序列](https://leetcode-cn.com/problems/distinct-subsequences/)

给定一个字符串 `s` 和一个字符串 `t` ，计算在 `s` 的子序列中 `t` 出现的个数。

字符串的一个 **子序列** 是指，通过删除一些（也可以不删除）字符且不干扰剩余字符相对位置所组成的新字符串。（例如，`"ACE"` 是 `"ABCDE"` 的一个子序列，而 `"AEC"` 不是）

题目数据保证答案符合 32 位带符号整数范围。

**示例 1：**

```
输入：s = "rabbbit", t = "rabbit"
输出：3
解释：
如下图所示, 有 3 种可以从 s 中得到 "rabbit" 的方案。
rabbbit
rabbbit
rabbbit
```

**示例 2：**

```
输入：s = "babgbag", t = "bag"
输出：5
解释：
如下图所示, 有 5 种可以从 s 中得到 "bag" 的方案。 
babgbag
babgbag
babgbag
babgbag
babgbag
```

**提示：**

- `0 <= s.length, t.length <= 1000`
- `s` 和 `t` 由英文字母组成

### 题目解法

```
package algorithm;

import java.util.Arrays;

public class DistinctSubsequences {

    public static int numDistinct(String s, String t) {
        int m = t.length();
        int n = s.length();
        int[][] array = new int[m + 1][n + 1];
        Arrays.fill(array[0], 1);
        for (int i = 1; i < m + 1; i++) {
            for (int j = 1; j < n + 1; j++) {
                if (s.charAt(j - 1) == t.charAt(i - 1)) {
                    array[i][j] = array[i][j - 1] + array[i - 1][j - 1];
                } else {
                    array[i][j] = array[i][j - 1];
                }
            }
        }
        return array[m][n];
    }

    public static void main(String[] args) {
        numDistinct("rabbbit", "rabbit");
        System.out.println();
    }
}
```

打印：

```

```

**思路：**

思路上，很简单。动态规划。不过我的效率一般，需要优化。