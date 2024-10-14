---
title: PermutationSequence
date: 2021-04-11 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 排列序列

- [题目介绍](https://yangtzeshore.github.io/2021/04/11/PermutationSequence/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/04/11/PermutationSequence/#题目解法)

### 题目介绍

#### [排列序列](https://leetcode-cn.com/problems/permutation-sequence/)

给出集合 `[1,2,3,...,n]`，其所有元素共有 `n!` 种排列。

按大小顺序列出所有排列情况，并一一标记，当 `n = 3` 时, 所有排列如下：

1. `"123"`
2. `"132"`
3. `"213"`
4. `"231"`
5. `"312"`
6. `"321"`

给定 `n` 和 `k`，返回第 `k` 个排列。

**示例1：**

```
输入：n = 3, k = 3
输出："213"
```

**示例2：**

```
输入：n = 4, k = 9
输出："2314"
```

**示例3：**

```
输入：n = 3, k = 1
输出："123"
```

**提示：**

- `1 <= n <= 9`
- `1 <= k <= n!`

### 题目解法

```
package algorithm;

import java.util.Arrays;

public class PermutationSequence {

    public static String getPermutation(int n, int k) {
        int[] factorial = new int[n];
        factorial[0] = 1;
        // 记录n!有多少个数字组合
        for (int i = 1; i < n; ++i) {
            factorial[i] = factorial[i - 1] * i;
        }

        --k;
        StringBuffer ans = new StringBuffer();
        int[] valid = new int[n + 1];
        Arrays.fill(valid, 1);
        for (int i = 1; i <= n; ++i) {
            // 找到第一个位置，k-1是为了防止整除越界，因为一定会加1
            // 如果k整除又加1，会最后一个多一位，下面也是如此
            int order = k / factorial[n - i] + 1;
            // 记录目前使用的数字，已经使用过的当然跳过，
            // 不是说order等于3就选3，而是选目前剩下的排第三的，
            // 所以这个order算法太巧妙了
            for (int j = 1; j <= n; ++j) {
                order -= valid[j];
                if (order == 0) {
                    ans.append(j);
                    valid[j] = 0;
                    break;
                }
            }
            // 注意是i，不是1，这里没有用公式+1，相当于k自动减一，很巧妙
            k %= factorial[n - i];
        }
        return ans.toString();
    }

    public static void main(String[] args) {
        System.out.println(getPermutation(3, 3));
        System.out.println(getPermutation(4, 9));
        System.out.println(getPermutation(3, 1));
    }
}
```

打印：

```
213
2314
123
```

**思路：**

思路上，就是一道数学题呗。不过，官方的解题技巧太妙了，可以反复看。