---
title: MinimumWindowSubstring
date: 2021-05-18 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 最小覆盖子串

- [题目介绍](https://yangtzeshore.github.io/2021/05/18/MinimumWindowSubstring/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/05/18/MinimumWindowSubstring/#题目解法)

### 题目介绍

#### [最小覆盖子串](https://leetcode-cn.com/problems/minimum-window-substring/)

给你一个字符串 `s` 、一个字符串 `t` 。返回 `s` 中涵盖 `t` 所有字符的最小子串。如果 `s` 中不存在涵盖 `t` 所有字符的子串，则返回空字符串 `""` 。

**注意：**如果 `s` 中存在这样的子串，我们保证它是唯一的答案。

**示例1：**

```
输入：s = "ADOBECODEBANC", t = "ABC"
输出："BANC"
```

**示例1：**

```
输入：s = "a", t = "a"
输出："a"
```

**提示：**

- `1 <= s.length, t.length <= 105`
- `s` 和 `t` 由英文字母组成

### 题目解法

```
package algorithm;

import java.util.HashMap;
import java.util.Map;

public class MinimumWindowSubstring {

    static Map<Character, Integer> ori = new HashMap<>();
    static Map<Character, Integer> cnt = new HashMap<>();

    public static String minWindow(String s, String t) {
        int tLen = t.length();
        for (int i = 0; i < tLen; i++) {
            char c = t.charAt(i);
            ori.put(c, ori.getOrDefault(c, 0) + 1);
        }
        int l = 0, r = -1;
        int len = Integer.MAX_VALUE, ansL = -1, ansR = -1;
        int sLen = s.length();
        while (r < sLen) {
            ++r;
            if (r < sLen && ori.containsKey(s.charAt(r))) {
                cnt.put(s.charAt(r), cnt.getOrDefault(s.charAt(r), 0) + 1);
            }
            while (check() && l <= r) {
                if (r - l + 1 < len) {
                    len = r - l + 1;
                    ansL = l;
                    ansR = l + len;
                }
                if (ori.containsKey(s.charAt(l))) {
                    cnt.put(s.charAt(l), cnt.getOrDefault(s.charAt(l), 0) - 1);
                }
                ++l;
            }
        }
        return ansL == -1 ? "" : s.substring(ansL, ansR);
    }

    public static boolean check() {
        for (Map.Entry<Character, Integer> entry : ori.entrySet()) {
            Character key = entry.getKey();
            Integer val = entry.getValue();
            if (cnt.getOrDefault(key, 0) < val) {
                return false;
            }
        }
        return true;
    }

    public static void main(String[] args) {
//        System.out.println(minWindow("ADOBECODEBANC", "ABC"));

        System.out.println(minWindow("a", "a"));
    }
}
```

打印：

```
a
```

**思路：**

思路：官方的提示有点坑，需要O(n)的时间，但是明显答案不满足这个条件，所以没有找到好的解题方法，看了官方答案，才明白和自己想的差不多，双指针思路。