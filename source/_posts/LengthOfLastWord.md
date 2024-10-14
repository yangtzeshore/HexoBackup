---
title: LengthOfLastWord
date: 2021-04-06 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 最后一个单词的长度

- [题目介绍](https://yangtzeshore.github.io/2021/04/06/LengthOfLastWord/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/04/06/LengthOfLastWord/#题目解法)

### 题目介绍

#### [最后一个单词的长度](https://leetcode-cn.com/problems/length-of-last-word/)

给你一个字符串 `s`，由若干单词组成，单词之间用空格隔开。返回字符串中最后一个单词的长度。如果不存在最后一个单词，请返回 0 。

**单词** 是指仅由字母组成、不包含任何空格字符的最大子字符串。

**示例1：**

```
输入：s = "Hello World"
输出：5
```

**示例2：**

```
输入：s = " "
输出：0
```

**提示：**

- 1<=s.length<=104
- `s` 仅有英文字母和空格 `' '` 组成

### 题目解法

```
package algorithm;

public class LengthOfLastWord {

    public static int lengthOfLastWord(String s) {

        int end = s.length() - 1;
        while (end >= 0 && s.charAt(end) == ' ') {
            end--;
        }
        if (end < 0) {
            return 0;
        }
        int start = end;
        while (start >= 0 && s.charAt(start) != ' ') {
            start--;
        }
        return end - start;
    }

    public static void main(String[] args) {
        System.out.println(lengthOfLastWord("Hello World"));

        System.out.println(lengthOfLastWord(" "));
    }
}
```

打印：

```
5
0
```

**思路：**

思路上，属于简单题目，看代码即可。