---
title: SimplifyPath
date: 2021-05-04 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 简化路径

- [题目介绍](https://yangtzeshore.github.io/2021/05/04/SimplifyPath/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/05/04/SimplifyPath/#题目解法)

### 题目介绍

#### [简化路径](https://leetcode-cn.com/problems/simplify-path/)

给你一个字符串 `path` ，表示指向某一文件或目录的 Unix 风格 **绝对路径** （以 `'/'` 开头），请你将其转化为更加简洁的规范路径。

在 Unix 风格的文件系统中，一个点（`.`）表示当前目录本身；此外，两个点 （`..`） 表示将目录切换到上一级（指向父目录）；两者都可以是复杂在 Unix 风格的文件系统中，一个点（`.`）表示当前目录本身；此外，两个点 （`..`） 表示将目录切换到上一级（指向父目录）；两者都可以是复杂。

请注意，返回的 **规范路径** 必须遵循下述格式：

- 始终以斜杠 `'/'` 开头。
- 两个目录名之间必须只有一个斜杠 `'/'` 。
- 最后一个目录名（如果存在）**不能** 以 `'/'` 结尾。
- 此外，路径仅包含从根目录到目标文件或目录的路径上的目录（即，不含 `'.'` 或 `'..'`）。

返回简化后得到的 **规范路径** 。

**示例1：**

```
输入：path = "/home/"
输出："/home"
解释：注意，最后一个目录名后面没有斜杠。 
```

**示例2：**

```
输入：path = "/../"
输出："/"
解释：从根目录向上一级是不可行的，因为根目录是你可以到达的最高级。
```

**示例3：**

```
输入：path = "/home//foo/"
输出："/home/foo"
解释：在规范路径中，多个连续斜杠需要用一个斜杠替换。
```

**示例4：**

```
输入：path = "/a/./b/../../c/"
输出："/c"
```

**提示：**

- `1 <= path.length <= 3000`
- `path` 由英文字母，数字，`'.'`，`'/'` 或 `'_'` 组成。
- `path` 是一个有效的 Unix 风格绝对路径。

### 题目解法

```
package algorithm;

import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;

public class SimplifyPath {

    public static String simplifyPath(String path) {

        LinkedList<String> stack = new LinkedList<>();
        char[] chars = path.toCharArray();
        List<String> strings = new ArrayList<>();
        int start = 0;
        for (int end = 0; end < chars.length; end++) {
            if (chars[end] == '/') {
                if (end > start) {
                    strings.add(path.substring(start, end));
                }
                strings.add("/");
                start = end + 1;
            } else if (end == chars.length - 1) {
                strings.add(path.substring(start, end + 1));
            }
        }

        for (String string : strings) {
            switch (string) {
                case "/":
                    if (stack.size() > 0 && stack.get(stack.size() - 1).equals("/")) {
                        continue;
                    } else {
                        stack.add(string);
                    }
                    break;
                case ".":
                    break;
                case "..":
                    if (stack.size() > 2) {
                        stack.removeLast();
                        stack.removeLast();
                        continue;
                    }
                    break;
                default:
                    stack.add(string);
                    break;
            }
        }

        if (stack.size() > 1 && stack.getLast().equals("/")) {
            stack.removeLast();
        }

        StringBuilder stringBuilder = new StringBuilder();
        for (String s : stack) {
            stringBuilder.append(s);
        }
        return stringBuilder.toString();
    }

    public static void main(String[] args) {
        System.out.println(simplifyPath("/home"));
        System.out.println(simplifyPath("/../"));
        System.out.println(simplifyPath("/home//foo/"));
        System.out.println(simplifyPath("/a/./b/../../c/"));
    }
}
```

打印：

```
/home
/
/home/foo
/c
```

**思路：**

思路很简单，就是挨个匹配。