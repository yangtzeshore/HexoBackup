---
title: ValidParentheses
date: 2021-01-24 09:22:21
tags:
- 
categories:
- Leetcode 
---



## 有效的括号

- [题目介绍](https://yangtzeshore.github.io/2021/01/24/ValidParentheses/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/01/24/ValidParentheses/#题目解法)

### 题目介绍

[有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)

给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串，判断字符串是否有效。

有效字符串需满足：

1. 左括号必须用相同类型的右括号闭合。
2. 左括号必须以正确的顺序闭合。

注意空字符串可被认为是有效字符串。

**示例 1：**

```
输入: "()"
输出: true
```

**示例 2：**

```
输入: "()[]{}"
输出: true
```

**示例 3：**

```
输入: "(]"
输出: false
```

**示例 4：**

```
输入: "([)]"
输出: false
```

**示例 5：**

```
输入: "{[]}"
输出: true
```

### 题目解法

```
package algorithm;

import java.util.*;

public class ValidParentheses {

    public static boolean isValid(String s) {

        if (s.length() < 2 || s.length() % 2 == 1) {
            return false;
        }

        Map<Character, Character> map = new HashMap<Character, Character>(){{
            put('}', '{');
            put(']', '[');
            put(')', '(');
        }};

        char[] chars = s.toCharArray();
        Deque<Character> deque = new LinkedList<>();
        for (char aChar : chars) {
            if (map.get(aChar) != null && deque.size() >= 1 && deque.getLast() == map.get(aChar)) {
                deque.pollLast();
            } else {
                deque.add(aChar);
            }
        }
        return deque.size() <= 0;
    }

    public static void main(String[] args) {

        String s = "()";
        System.out.println(isValid(s));

        s = "()[]{}";
        System.out.println(isValid(s));

        s = "(]";
        System.out.println(isValid(s));

        s = "){";
        System.out.println(isValid(s));
    }
}
```

打印：

```
true
true
false
false
```

**思路：**

很简单，就是栈。不过java的栈最好不要用stack类，用queue的实现比较好。