---
title: 整数反转
date: 2021-01-04 09:22:21
tags:
- 
categories:
- Leetcode 
---



## 整数反转

- [题目介绍](https://yangtzeshore.github.io/2021/01/04/Reverse/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/01/04/Reverse/#题目解法)

### 题目介绍

[整数反转](https://leetcode-cn.com/problems/reverse-integer/)

给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

**示例 1:**

```
输入: 123
输出: 321
```

**示例 2:**

```
输入: -123
输出: -321
```

**示例 3:**

```
输入: 120
输出: 21
```

**注意:**

假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 [−231, 231 − 1]。请根据这个假设，如果反转后整数溢出那么就返回 0。

## 题目解法

这一版性能不是很好，可以参考下。

```
package algorithm;

public class Reverse {

    public static int reverse(int x) {

        if (x == 0) {
            return 0;
        }

        StringBuilder s = new StringBuilder();
        if (x < 0) {
            String will = String.valueOf(x).substring(1);
            s.append('-');
            boolean flag0 = true;
            for (int i = will.length() - 1, j = 1; i >= 0; i--) {
                if (will.charAt(i) == '0' && flag0) {
                    continue;
                }
                s.append(will.charAt(i));
                flag0 = false;
            }
        } else {
            String will = String.valueOf(x);
            boolean flag0 = true;
            for (int i = will.length() - 1, j = 0; i >= 0; i--) {
                if (will.charAt(i) == '0' && flag0) {
                    continue;
                }
                s.append(will.charAt(i));
                flag0 = false;
            }
        }

        if (checkOutOfRange(s.toString())) {
            return 0;
        }

        return Integer.parseInt(s.toString());
    }

    private static boolean checkOutOfRange(String will) {
        String max = String.valueOf(Integer.MAX_VALUE);
        String min = String.valueOf(Integer.MIN_VALUE);

        if (will.equals("0")) {
            return false;
        }

        if(will.startsWith("-")) {
            if (will.equals(min)) {
                return false;
            }
            if (will.length() > min.length()) {
                return true;
            }
            if (will.length() < min.length()) {
                return false;
            }
            for (int i = 0; i < will.length(); i++) {
                if ((int) will.charAt(i) > (int) min.charAt(i)) {
                    return true;
                } else if ((int) will.charAt(i) < (int) min.charAt(i)) {
                    return false;
                }
            }
            return false;
        }

        if (will.equals(max)) {
            return false;
        }
        if (will.length() > max.length()) {
            return true;
        }
        if (will.length() < max.length()) {
            return false;
        }
        for (int i = 0; i < will.length(); i++) {
            if ((int) will.charAt(i) > (int) max.charAt(i)) {
                return true;
            } else if ((int) will.charAt(i) < (int) max.charAt(i)) {
                return false;
            }
        }
        return false;
    }

    public static void main(String[] args) {

        int test = 123;
        System.out.println(reverse(test));
        test = -123;
        System.out.println(reverse(test));
        test = 120;
        System.out.println(reverse(test));
        test = 1534236469;
        System.out.println(reverse(test));
        test = -2147483648;
        System.out.println(reverse(test));
        test = -2147483412;
        System.out.println(reverse(test));

    }
}
```

打印：

```
321
-321
21
0
0
-2143847412
```

思路：

- 就是类似暴力破解。

比较优雅的一版如下：

```
public static int reverse1(int x) {
        int ans = 0;
        while (x != 0) {
            int pop = x % 10;
            // max == 2,147,483,647
            if (ans > Integer.MAX_VALUE / 10 || (ans == Integer.MAX_VALUE / 10 && pop > 7))
                return 0;
            // min == 2,147,483,648
            if (ans < Integer.MIN_VALUE / 10 || (ans == Integer.MIN_VALUE / 10 && pop < -8))
                return 0;
            ans = ans * 10 + pop;
            x /= 10;
        }
        return ans;
    }
```

思路：

- 就是拆解成取模算法，并且根据最大最小值做校验判断。