---
title: LongestCommonPrefix
date: 2021-01-19 09:22:21
tags:
- 
categories:
- Leetcode 
---



## 最长公共前缀

- [题目介绍](https://yangtzeshore.github.io/2021/01/19/LongestCommonPrefix/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/01/19/LongestCommonPrefix/#题目解法)

### 题目介绍

[最长公共前缀](https://leetcode-cn.com/problems/longest-common-prefix/)

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 `""`。

**示例 1:**

```
输入：strs = ["flower","flow","flight"]
输出："fl"
```

**示例 2:**

```
输入：strs = ["dog","racecar","car"]
输出：""
解释：输入不存在公共前缀。
```

**提示：**

- `0 <= strs.length <= 200`
- `0 <= strs[i].length <= 200`
- `strs[i]` 仅由小写英文字母组成

### 题目解法

```
package algorithm;

public class LongestCommonPrefix {

    public static String longestCommonPrefix(String[] strs) {

        if (strs.length == 0) {
            return "";
        }

        StringBuilder s = new StringBuilder();
        String str = strs[0];
        for (int i = 0; i < str.length(); i++) {
            boolean flag = true;
            char a = str.charAt(i);
            for (int j = 1; j < strs.length; j++) {
                if (i >= strs[j].length() || a != strs[j].charAt(i)) {
                    flag = false;
                    break;
                }
            }
            if (!flag) {
                break;
            }
            s.append(a);
        }
        return s.toString();
    }

    public static void main(String[] args) {
        String[] strs = new String[]{"flower","flow","flight"};
        System.out.println(longestCommonPrefix(strs));

        strs = new String[]{"dog","racecar","car"};
        System.out.println(longestCommonPrefix(strs));
    }
}
```

打印：

```
fl
```

**思路：**

很简单，就不说了。