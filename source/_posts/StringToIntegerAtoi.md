---
title: StringToIntegerAtoi
date: 2021-01-08 09:22:21
tags:
- 
categories:
- Leetcode 
---



## 字符串转换整数 (atoi)

- [题目介绍](https://yangtzeshore.github.io/2021/01/08/StringToIntegerAtoi/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/01/08/StringToIntegerAtoi/#题目解法)

### 题目介绍

[字符串转换整数 (atoi)](https://leetcode-cn.com/problems/string-to-integer-atoi/)

请你来实现一个 atoi 函数，使其能将字符串转换成整数。

首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。接下来的转化规则如下：

- 如果第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字字符组合起来，形成一个有符号整数。
- 假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成一个整数。
- 该字符串在有效的整数部分之后也可能会存在多余的字符，那么这些字符可以被忽略，它们对函数不应该造成影响。

注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换，即无法进行有效转换。

在任何情况下，若函数不能进行有效的转换时，请返回 0 。

**提示：**

- 本题中的空白字符只包括空格字符 ‘ ‘ 。
- 假设我们的环境只能存储 32 位大小的有符号整数，那么其数值范围为 [− 231, 231−1]。如果数值超过这个范围，请返回 INT_MAX ( 231−1 )或INT_MIN (− 231 ) 。

**示例 1：**

```
输入: "42"
输出: 42
```

**示例 2:**

```
输入: "   -42"
输出: -42
解释: 第一个非空白字符为 '-', 它是一个负号。
     我们尽可能将负号与后面所有连续出现的数字组合起来，最后得到 -42 。
```

**示例 3：**

```
输入: "4193 with words"
输出: 4193
解释: 转换截止于数字 '3' ，因为它的下一个字符不为数字。
```

**示例 4：**

```
输入: "words and 987"
输出: 0
解释: 第一个非空字符是 'w', 但它不是数字或正、负号。因此无法执行有效的转换。
```

**示例 5：**

```
输入: "-91283472332"
输出: -2147483648
解释: 数字 "-91283472332" 超过 32 位有符号整数范围。因此返回 INT_MIN (−231) 。
```

**提示：**

- 0 <= s.length <= 20
- 0 <= p.length <= 30
- s 可能为空，且只包含从 a-z 的小写字母。
- p 可能为空，且只包含从 a-z 的小写字母，以及字符 . 和 *。
- 保证每次出现字符 * 时，前面都匹配到有效的字符

## 题目解法

这道题没有做出来，参考了官方的解题思路，只要看懂了，写出自动机，解题并不难。

```
package algorithm;

import java.util.HashMap;
import java.util.Map;

public class StringToIntegerAtoi {
    public static int myAtoi(String str) {
        Automaton automaton = new Automaton();
        int length = str.length();
        for (int i = 0; i < length; ++i) {
            automaton.get(str.charAt(i));
        }
        return (int) (automaton.sign * automaton.ans);
    }


    public static void main(String[] args) {
        String s = "21474836460";
        System.out.println(myAtoi(s));

        s = "   -42";
        System.out.println(myAtoi(s));

        s = "4193 with words";
        System.out.println(myAtoi(s));

        s = "words and 987";
        System.out.println(myAtoi(s));

        s = "-91283472332";
        System.out.println(myAtoi(s));
    }
}

class Automaton {
    public int sign = 1;
    public long ans = 0;
    private String state = "start";
    private Map<String, String[]> table = new HashMap<String, String[]>() {{
        put("start", new String[]{"start", "signed", "in_number", "end"});
        put("signed", new String[]{"end", "end", "in_number", "end"});
        put("in_number", new String[]{"end", "end", "in_number", "end"});
        put("end", new String[]{"end", "end", "end", "end"});
    }};

    public void get(char c) {
        state = table.get(state)[get_col(c)];
        if ("in_number".equals(state)) {
            ans = ans * 10 + c - '0';
            ans = sign == 1 ? Math.min(ans, (long) Integer.MAX_VALUE) : Math.min(ans, -(long) Integer.MIN_VALUE);
        } else if ("signed".equals(state)) {
            sign = c == '+' ? 1 : -1;
        }
    }

    private int get_col(char c) {
        if (c == ' ') {
            return 0;
        }
        if (c == '+' || c == '-') {
            return 1;
        }
        if (Character.isDigit(c)) {
            return 2;
        }
        return 3;
    }
}
```

打印：

```
2147483647
-42
4193
0
-2147483648
```

**思路：**

**自动机**

字符串处理的题目往往涉及复杂的流程以及条件情况，如果直接上手写程序，一不小心就会写出极其臃肿的代码。

因此，为了有条理地分析每个输入字符的处理方法，我们可以使用自动机这个概念：

我们的程序在每个时刻有一个状态 s，每次从序列中输入一个字符 c，并根据字符 c 转移到下一个状态 s’。这样，我们只需要建立一个覆盖所有情况的从 s 与 c 映射到 s’ 的表格即可解决题目中的问题。

**算法**

本题可以建立如下图所示的自动机：

![自动机](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/%E8%87%AA%E5%8A%A8%E6%9C%BA.png)

我们也可以用下面的表格来表示这个自动机：

|           |  ‘ ‘  |  +/-   |  number   | other |
| :-------: | :---: | :----: | :-------: | :---: |
|   start   | start | signed | in_number |  end  |
|  signed   |  end  |  end   | in_number |  end  |
| in_number |  end  |  end   | in_number |  end  |
|    end    |  end  |  end   |    end    |  end  |

接下来编程部分就非常简单了：我们只需要把上面这个状态转换表抄进代码即可。

另外自动机也需要记录当前已经输入的数字，只要在 s’ 为 in_number 时，更新我们输入的数字，即可最终得到输入的数字。

注意：只有5个状态是有效的。

**复杂度分析**

**时间复杂度**：O(n)，其中 n为字符串的长度。我们只需要依次处理所有的字符，处理每个字符需要的时间为 O(1)。

**空间复杂度**：O(1)，自动机的状态只需要常数空间存储。