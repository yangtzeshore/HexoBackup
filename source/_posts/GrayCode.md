---
title: GrayCode
date: 2021-06-17 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 格雷编码

- [题目介绍](https://yangtzeshore.github.io/2021/06/17/GrayCode/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/06/17/GrayCode/#题目解法)

### 题目介绍

#### [格雷编码](https://leetcode-cn.com/problems/gray-code/)

格雷编码是一个二进制数字系统，在该系统中，两个连续的数值仅有一个位数的差异。

给定一个代表编码总位数的非负整数 *n*，打印其格雷编码序列。即使有多个不同答案，你也只需要返回其中一种。

格雷编码序列必须以 0 开头。

**示例1：**

```
输入: 2
输出: [0,1,3,2]
解释:
00 - 0
01 - 1
11 - 3
10 - 2

对于给定的 n，其格雷编码序列并不唯一。
例如，[0,2,3,1] 也是一个有效的格雷编码序列。

00 - 0
10 - 2
11 - 3
01 - 1
```

**示例 2：**

```
输入: 0
输出: [0]
解释: 我们定义格雷编码序列必须以 0 开头。
     给定编码总位数为 n 的格雷编码序列，其长度为 2n。当 n = 0 时，长度为 20 = 1。
     因此，当 n = 0 时，其格雷编码序列为 [0]。
```

### 题目解法

```
package algorithm;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class GrayCode {

    public static List<Integer> grayCode(int n) {
        if (n == 0) {
            return Collections.singletonList(0);
        }

        int length = (int) Math.pow(2, n);
        int[][] arr = new int[length][n];
        arr[1][n - 1] = 1;
        for (int i = 2; i < length; i++) {
            if (i % 2 == 0) {
                for (int j = n - 1; j >= 0; j--) {
                    if (arr[i - 1][j] == 1) {
                        System.arraycopy(arr[i - 1],0, arr[i],0, n);
                        arr[i][j - 1] = arr[i][j - 1] ^ 1;
                        break;
                    }
                }

            } else {
                System.arraycopy(arr[i - 1],0, arr[i],0, n);
                arr[i][n - 1] = arr[i][n - 1] ^ 1;
            }
        }
        List<Integer> ans = new ArrayList<>();
        for (int[] ints : arr) {
            int result = 0;
            for (int i = 0; i < ints.length; i++) {
                result += ints[i] * Math.pow(2, ints.length - i - 1);
            }
            ans.add(result);
        }
        return ans;
    }

    // 镜像法
    public List<Integer> grayCode1(int n) {
        List<Integer> res = new ArrayList<Integer>() {{ add(0); }};
        int head = 1;
        // 多少轮镜像，这个要看维基了解下
        for (int i = 0; i < n; i++) {
            // 每次都要遍历一遍已有集合，用镜像法算出本轮的值：开头1+已有值，
            // 值得注意，每轮都会留下上一轮的数据
            for (int j = res.size() - 1; j >= 0; j--) {
                res.add(head + res.get(j));
            }
            // head = head << 1; 相当于每次加了一个1开头的大数，完成镜像
            head <<= 1;
        }
        return res;
    }

    public static void main(String[] args) {
        System.out.println(grayCode(2));

        System.out.println(grayCode(0));
    }
}
```

打印：

```
[0, 1, 3, 2]
[0]
```

**思路：**

思路上，我用了暴力破解。当然，dp我也想到了，但是没有好的办法实现。格雷码还是需要看下维基，了解下，才好写解法。