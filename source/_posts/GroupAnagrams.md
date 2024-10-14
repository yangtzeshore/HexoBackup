---
title: GroupAnagrams
date: 2021-03-16 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 字母异位词分组

- [题目介绍](https://yangtzeshore.github.io/2021/03/16/GroupAnagrams/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/03/16/GroupAnagrams/#题目解法)

### 题目介绍

#### [字母异位词分组](https://leetcode-cn.com/problems/group-anagrams/)

给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。

**示例1：**

```
输入: ["eat", "tea", "tan", "ate", "nat", "bat"]
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]
```

**说明**

- 所有输入均为小写字母。
- 不考虑答案输出的顺序。

### 题目解法

```
package algorithm;

import java.util.*;

public class GroupAnagrams {

    public static List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<>();
        for (String str : strs) {
            char[] array = str.toCharArray();
            Arrays.sort(array);
            String key = new String(array);
            List<String> list = map.getOrDefault(key, new ArrayList<>());
            list.add(str);
            map.put(key, list);
        }
        return new ArrayList<>(map.values());
    }

    public static void main(String[] args) {
        String[] strs = new String[]{"eat", "tea", "tan", "ate", "nat", "bat"};
        System.out.println(groupAnagrams(strs));
    }
}
```

打印：

```
[[eat, tea, ate], [bat], [tan, nat]]
```

**思路：**

思路上，没想到官方的思路这么简单，而且效率这么低。