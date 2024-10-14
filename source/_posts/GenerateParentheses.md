---
title: GenerateParentheses
date: 2021-01-25 09:22:21
tags:
- 
categories:
- Leetcode 
---



## 括号生成

- [题目介绍](https://yangtzeshore.github.io/2021/01/25/GenerateParentheses/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/01/25/GenerateParentheses/#题目解法)

### 题目介绍

[括号生成](https://leetcode-cn.com/problems/generate-parentheses/)

数字 `n` 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 **有效的** 括号组合。

**示例 1：**

```
输入：n = 3
输出：["((()))","(()())","(())()","()(())","()()()"]
```

**示例 2：**

```
输入：n = 1
输出：["()"]
```

**提示：**

- `1 <= n <= 8`

### 题目解法

```
package algorithm;

import java.util.ArrayList;
import java.util.List;

public class GenerateParentheses {

    // n<=8，所以最多也就64，因为开头结尾必须()
    static ArrayList[] cache = new ArrayList[100];

    public static List<String> generate(int n) {
        if (cache[n] != null) {
            return cache[n];
        }
        ArrayList<String> ans = new ArrayList<>();
        if (n == 0) {
            // 长度为0当然是空
            ans.add("");
        } else {
            // c表示几对，因为已经有了默认的一对，所以小于n
            for (int c = 0; c < n; ++c) {
                for (String left : generate(c)) {
                    // 最大也就n-1，因为已经有了默认的一对
                    for (String right: generate(n - 1 - c)) {
                        // 一对默认的，和左边的一对，右边的一对
                        ans.add("(" + left + ")" + right);
                    }
                }
            }
        }
        cache[n] = ans;
        return ans;
    }

    public static List<String> generateParenthesis(int n) {
        return generate(n);
    }

    public static void main(String[] args) {

        System.out.println(generate(3));

        System.out.println(generate(1));
    }
}
```

打印：

```
[()()(), ()(()), (())(), (()()), ((()))]
[()]
```

**思路：**

本来以为是很难的，官方的三种思路都想过，自己算了下复杂度，感觉不对，没想到三种都想到了。也就是，一个生成，一个校验，这是第一种；校验的过程再给个优化，括号数量配对，这是第二种；开头结尾必须是()这两个，这是第三种，然后通过拼接分割右括号的左右字符串完成。