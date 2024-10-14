---
title: Pow(x, n)
date: 2021-03-20 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### Pow(x, n)

- [题目介绍](https://yangtzeshore.github.io/2021/03/20/PowxN/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/03/20/PowxN/#题目解法)

### 题目介绍

#### [Pow(x, n)](https://leetcode-cn.com/problems/powx-n/)

实现 [pow(*x*, *n*)](https://www.cplusplus.com/reference/valarray/pow/) ，即计算 x 的 n 次幂函数（即，xn）。

**示例1：**

```
输入：x = 2.00000, n = 10
输出：1024.00000
```

**示例2：**

```
输入：x = 2.10000, n = 3
输出：9.26100
```

**示例3：**

```
输入：x = 2.00000, n = -2
输出：0.25000
解释：2-2 = 1/22 = 1/4 = 0.25
```

**说明**

- `-100.0 < x < 100.0`
- −231<=n<=231−1
- −104<=xn<=104

### 题目解法

```
package algorithm;

public class PowxN {

    public static double myPow(double x, int n) {

        return n >= 0 ? getMulti(x, n) :  1.0 / getMulti(x, -(long) n);
    }

    private static double getMulti(double x, long n) {

        if (n == 0) {
            return 1.0;
        }

        double multi = getMulti(x, n / 2);
        return n % 2 == 1 ? multi * multi * x : multi * multi;
    }

    public static void main(String[] args) {

        double x = 2.00000;
        int n = 10;
        System.out.println(myPow(x, n));

        x = 2.10000;
        n = 3;
        System.out.println(myPow(x, n));

        x = 2.00000;
        n = -2;
        System.out.println(myPow(x, n));
    }
}
```

打印：

```
1024.0
9.261000000000001
0.25
```

**思路：**

思路上，就是递归和二分。