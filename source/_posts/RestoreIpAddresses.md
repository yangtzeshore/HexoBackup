---
title: RestoreIpAddresses
date: 2021-06-27 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 复原 IP 地址

- [题目介绍](https://yangtzeshore.github.io/2021/06/26/RestoreIpAddresses/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/06/26/RestoreIpAddresses/#题目解法)

### 题目介绍

#### [复原 IP 地址](https://leetcode-cn.com/problems/restore-ip-addresses/)

给定一个只包含数字的字符串，用以表示一个 IP 地址，返回所有可能从 `s` 获得的 **有效 IP 地址** 。你可以按任何顺序返回答案。

**有效 IP 地址** 正好由四个整数（每个整数位于 0 到 255 之间组成，且不能含有前导 `0`），整数之间用 `'.'` 分隔。

例如：”0.1.2.201” 和 “192.168.1.1” 是 **有效** IP 地址，但是”0.011.255.245”、”192.168.1.312” 和 “192.168@1.1” 是 **无效** IP 地址。

**示例 1：**

```
输入：s = "25525511135"
输出：["255.255.11.135","255.255.111.35"]
```

**示例 2：**

```
输入：s = "0000"
输出：["0.0.0.0"]
```

**示例 3：**

```
输入：s = "1111"
输出：["1.1.1.1"]
```

**示例 4：**

```
输入：s = "010010"
输出：["0.10.0.10","0.100.1.0"]
```

**示例 5：**

```
输入：s = "101023"
输出：["1.0.10.23","1.0.102.3","10.1.0.23","10.10.2.3","101.0.2.3"]
```

**提示：**

- `0 <= s.length <= 3000`
- `s` 仅由数字组成

### 题目解法

```
package algorithm;

import java.util.ArrayList;
import java.util.List;

public class RestoreIpAddresses {

    static final int SEG_COUNT = 4;
    List<String> ans = new ArrayList<>();
    int[] segments = new int[SEG_COUNT];

    public List<String> restoreIpAddresses(String s) {
        segments = new int[SEG_COUNT];
        dfs(s, 0, 0);
        return ans;
    }

    public void dfs(String s, int segId, int segStart) {
        // 如果找到了 4 段 IP 地址并且遍历完了字符串，那么就是一种答案
        if (segId == SEG_COUNT) {
            if (segStart == s.length()) {
                StringBuffer ipAddr = new StringBuffer();
                for (int i = 0; i < SEG_COUNT; ++i) {
                    ipAddr.append(segments[i]);
                    if (i != SEG_COUNT - 1) {
                        ipAddr.append('.');
                    }
                }
                ans.add(ipAddr.toString());
            }
            return;
        }

        // 如果还没有找到 4 段 IP 地址就已经遍历完了字符串，那么提前回溯
        if (segStart == s.length()) {
            return;
        }

        // 由于不能有前导零，如果当前数字为 0，那么这一段 IP 地址只能为 0
        if (s.charAt(segStart) == '0') {
            segments[segId] = 0;
            dfs(s, segId + 1, segStart + 1);
        }

        // 一般情况，枚举每一种可能性并递归
        int addr = 0;
        for (int segEnd = segStart; segEnd < s.length(); ++segEnd) {
            addr = addr * 10 + (s.charAt(segEnd) - '0');
            if (addr > 0 && addr <= 0xFF) {
                segments[segId] = addr;
                dfs(s, segId + 1, segEnd + 1);
            } else {
                break;
            }
        }
    }

    public static void main(String[] args) {

    }
}
```

打印：

```
[255.255.11.135, 255.255.111.35]
[0.0.0.0]
[1.1.1.1]
[0.10.0.10, 0.100.1.0]
[1.0.10.23, 1.0.102.3, 10.1.0.23, 10.10.2.3, 101.0.2.3]
```

**思路：**

思路上，面向答案编程。虽然想到了大致的思路，但是dfs一致是弱项，最近算法写的都很烂，看来这一段时间的题目，都只能积累了，单凭简单的训练还是不够的。