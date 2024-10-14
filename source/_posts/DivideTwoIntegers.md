---
title: DivideTwoIntegers
date: 2021-02-01 09:22:21
tags:
- 
categories:
- Leetcode 
---



## 两数相除

- [题目介绍](https://yangtzeshore.github.io/2021/02/01/DivideTwoIntegers/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/02/01/DivideTwoIntegers/#题目解法)

### 题目介绍

#### [两数相除](https://leetcode-cn.com/problems/divide-two-integers/)

给定两个整数，被除数 `dividend` 和除数 `divisor`。将两数相除，要求不使用乘法、除法和 mod 运算符。

返回被除数 `dividend` 除以除数 `divisor` 得到的商。

整数除法的结果应当截去（`truncate`）其小数部分，例如：truncate(8.345) = 8`以及`truncate(-2.7335) = -2

**示例 1：**

```
输入: dividend = 10, divisor = 3
输出: 3
解释: 10/3 = truncate(3.33333..) = truncate(3) = 3
```

**示例 2：**

```
输入: dividend = 7, divisor = -3
输出: -2
解释: 7/-3 = truncate(-2.33333..) = -2
```

**提示：**

- 被除数和除数均为 32 位有符号整数。
- 除数不为 0。
- 假设我们的环境只能存储 32 位有符号整数，其数值范围是[−231,231−1]。本题中，如果除法结果溢出，则返回231−1。

### 题目解法

```
package algorithm;

public class DivideTwoIntegers {

    public static void main(String[] args) {

        // 越界后，只能返回-2147483648
        System.out.println(-(-2147483648));
        System.out.println((-2147483648 - 1));

        System.out.println(0 << 1);
    }

    int getBits(int num) {
        int numLen = 0;
        while (num != 0) {
            numLen++;
            num = num >> 1;
        }
        return numLen;
    }

    int getBit(int num, int pos) {//获取从右到左的第pos位置的值1/0
        pos = pos - 1;
        int index = 1 << pos;
        if ((num & index) >> pos != 0)
            return 1;
        else
            return 0;
    }

    int divide(int dividend, int divisor) {

        boolean minus = false;
        // 被除数是否是最大值
        boolean minValue = false;

        if (dividend < 0 || divisor < 0) {
            if (dividend < 0 && divisor < 0) {
                dividend = -dividend;
                divisor = -divisor;
            } else {
                dividend = dividend < 0 ? -dividend : dividend;
                divisor = divisor < 0 ? -divisor : divisor;
                minus = true;
            }

            if (dividend == -2147483648) {
                minValue = true;
                dividend = dividend - 1;
            }
            if (divisor == -2147483648) {
                // 被除数与除数相同
                if (minValue)
                    return 1;
                // 任何数除以最大值都是0，不区分符号了
                return 0;
            }
        }

        if (dividend < divisor)
            return 0;

        if (divisor == 1) {
            if (minus) {
                // 被除数为最大值
                if (minValue)
                    return -dividend - 1;
                else
                    return -dividend;
            } else
                return dividend;
        }

        // 二进制数头部可以除的数，每次用减法代替除，然后留下余数
        int divide = 0;
        // 目前除法或者说减法得到的结果
        int res = 0;
        int index = getBits(dividend);
        while (index > 0) {
            // 从头部用二进制减去除数，余数留下来
            int val = getBit(dividend, index--);
            divide = (divide << 1) + val;

            if (divide < divisor)
                res = res << 1;
            else {
                res = (res << 1) + 1;
                divide = divide - divisor;
            }
        }
        // -2147483648 2  因为最大数，转成正数后，少了1，所以如果余数加1能够被整除，当然加1
        if (minValue && (divide + 1) >= divisor) {
            res++;
        }
        if (minus) {
            return -res;
        }
        return res;
    }
}
```

打印：

```
-2147483648
2147483647
0
```

**思路：**

这道题没做出来。主要考察了怎么用减法代替除法，这个倒好理解。主要是最大最小值，这个边界问题，困扰一很久。

根据文档：

int数据类型是32位带符号的二进制补码整数。最小值为-2,147,483,648(0x80000000)，最大值为2,147,483,647(0x7FFFFFFF)(含)。

因此，当您将1加到整数的最大值时：

0x7FFFFFFF + 0x00000001 = 0x80000000(-2,147,483,648)

也就是所谓的最大+1等于最小，最小-1等于最大。最小乘以-1还是最小，因为越界了，刚好是最大+1。