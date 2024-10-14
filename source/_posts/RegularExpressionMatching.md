---
title: RegularExpressionMatching
date: 2021-01-07 09:22:21
tags:
- 
categories:
- Leetcode 
---



## 正则表达式匹配

- [题目介绍](https://yangtzeshore.github.io/2021/01/07/RegularExpressionMatching/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/01/07/RegularExpressionMatching/#题目解法)

### 题目介绍

[正则表达式匹配](https://leetcode-cn.com/problems/regular-expression-matching/)

给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 ‘.’ 和 ‘*’ 的正则表达式匹配。

- ‘.’ 匹配任意单个字符
- ‘*’ 匹配零个或多个前面的那一个元素

所谓匹配，是要涵盖 整个 字符串 s的，而不是部分字符串。

**示例 1：**

```
输入：s = "aa" p = "a"
输出：false
解释："a" 无法匹配 "aa" 整个字符串。
```

**示例 2:**

```
输入：s = "aa" p = "a*"
输出：true
解释：因为 '*' 代表可以匹配零个或多个前面的那一个元素, 在这里前面的元素就是 'a'。因此，字符串 "aa" 可被视为 'a' 重复了一次。
```

**示例 3：**

```
输入：s = "ab" p = ".*"
输出：true
解释：".*" 表示可匹配零个或多个（'*'）任意字符（'.'）。
```

**示例 4：**

```
输入：s = "aab" p = "c*a*b"
输出：true
解释：因为 '*' 表示零个或多个，这里 'c' 为 0 个, 'a' 被重复一次。因此可以匹配字符串 "aab"。
```

**示例 5：**

```
输入：s = "mississippi" p = "mis*is*p*."
输出：false
```

**提示：**

- 0 <= s.length <= 20
- 0 <= p.length <= 30
- s 可能为空，且只包含从 a-z 的小写字母。
- p 可能为空，且只包含从 a-z 的小写字母，以及字符 . 和 *。
- 保证每次出现字符 * 时，前面都匹配到有效的字符

## 题目解法

这道题没有做出来，参考了官方的解题思路，只要看懂了，写出动态方程，解题并不难。如果是感性的字符匹配理解的话，很容易陷入死循环的理解和字符匹配泥沼。

```
package algorithm;

public class RegularExpressionMatching {

    public static void main(String[] args) {
        String s = "aa", p = "a";
        System.out.print(isMatch(s, p));

        s = "aa"; p = "a*";
        System.out.print(isMatch(s, p));

        s = "ab"; p = ".*";
        System.out.print(isMatch(s, p));

        s = "aab"; p = "c*a*b";
        System.out.print(isMatch(s, p));

        s = "mississippi"; p = "mis*is*p*.";
        System.out.print(isMatch(s, p));
    }

    public static boolean isMatch(String s, String p) {
        int m = s.length();
        int n = p.length();

        // 注意初始化都是false
        boolean[][] f = new boolean[m + 1][n + 1];
        f[0][0] = true;
        for (int i = 0; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                if (p.charAt(j - 1) == '*') {
                    // 对着公式，这是缩减写法
                    f[i][j] = f[i][j - 2];
                    if (matches(s, p, i, j - 1)) {
                        f[i][j] = f[i][j] || f[i - 1][j];
                    }
                } else {
                    if (matches(s, p, i, j)) {
                        f[i][j] = f[i - 1][j - 1];
                    }
                }
            }
        }
        return f[m][n];
    }

    // 这里比较的一定是字符情况，所以不必考虑星号；且j一定是大于1
    public static boolean matches(String s, String p, int i, int j) {
        if (i == 0) {
            return false;
        }
        if (p.charAt(j - 1) == '.') {
            return true;
        }
        return s.charAt(i - 1) == p.charAt(j - 1);
    }

}
```

打印：

```
false
true
true
true
false
```

**思路：**

题目中的匹配是一个「逐步匹配」的过程：我们每次从字符串 `p` 中取出一个字符或者「字符 + 星号」的组合，并在 `s` 中进行匹配。对于 `p` 中一个字符而言，它只能在`s`中匹配一个字符，匹配的方法具有唯一性；而对于 `p` 中字符 + 星号的组合而言，它可以在 `s` 中匹配任意自然数个字符，并不具有唯一性。因此我们可以考虑使用动态规划，对匹配的方案进行枚举。

我们用 `f[i][j]`表示 `s` 的前`i`个字符与`p` 中的前`j`个字符是否能够匹配。在进行状态转移时，我们考虑`p`的第`j`个字符的匹配情况：

- 如果`p`的第`j`个字符是一个小写字母，那么我们必须在`s`中匹配一个相同的小写字母，即，，f[i][j]={f[i−1][j−1]，s[i]=p[j]false，s[i]≠p[j]

也就是说，如果s的第i个字符与p 的第j个字符不相同，那么无法进行匹配；否则我们可以匹配两个字符串的最后一个字符，完整的匹配结果取决于两个字符串前面的部分。

- 如果p 的第j个字符是 `*`，那么就表示我们可以对 p 的第j−1 个字符匹配任意自然数次。在匹配 0 次的情况下，我们有f[i][j]=f[i][j−2]

也就是我们「浪费」了一个字符 + 星号的组合，没有匹配任何 s中的字符。

在匹配 1,2,3,⋯ 次的情况下，类似地我们有

{f[i][j]=f[i−1][j−1],ifs[i]=p[j−1]f[i][j]=f[i−2][j−1],ifs[i−1]=s[i]=p[j−1]f[i][j]=f[i−3][j−1],ifs[i−2]=s[i−1]=s[i]=p[j−1]...

如果我们通过这种方法进行转移，那么我们就需要枚举这个组合到底匹配了s中的几个字符，会增导致时间复杂度增加，并且代码编写起来十分麻烦。我们不妨换个角度考虑这个问题：字母 + 星号的组合在匹配的过程中，本质上只会有两种情况：

- 匹配 s末尾的一个字符，将该字符扔掉，而该组合还可以继续进行匹配；
- 不匹配字符，将该组合扔掉，不再进行匹配。

如果按照这个角度进行思考，我们可以写出很精巧的状态转移方程：

，，f[i][j]={f[i−1][j]orf[i][j−2]，s[i]=p[j−1]f[i][j−2]，s[i]≠p[j]

右边的很好理解，就是没有匹配的情况。左边的需要结合实例，理解下：

```
aaaa*
aaa

aaa*
aaa

aaa*
aaaa
```

- 在任意情况下，只要p[j] 是 .，那么一定p[j]成功匹配中的任 s意一个小写字母。

最终的状态转移方程如下：

，，，，f[i][j]={if(p[j]≠′∗′)={f[i−1][j−1]，matchs(s[i],p[j])false，otherwiseotherwise={f[i−1][j]orf[i][j−2]，matchs(s[i],p[j−1])f[i][j−2]，otherwise

其中 matches(x,y)判断两个字符是否匹配的辅助函数。只有当 y是 . 或者 x和y本身相同时，这两个字符才会匹配。

**细节**

动态规划的边界条件为 f[0][0]=true，即两个空字符串是可以匹配的。最终的答案即为 f[m][n]，其中 m 和 n 分别是字符串 s和 p的长度。由于大部分语言中，字符串的字符下标是从 0 开始的，因此在实现上面的状态转移方程时，需要注意状态中每一维下标与实际字符下标的对应关系。

在上面的状态转移方程中，如果字符串 p中包含一个「字符 + 星号」的组合（例如 `a*` ），那么在进行状态转移时，会先将 a 进行匹配（当 p[j]为 a 时），再将`a*` 作为整体进行匹配（当 p[j]为 `*`时）。然而，在题目描述中，我们必须将 `a*` 看成一个整体，因此将 a 进行匹配是不符合题目要求的。看来我们进行了额外的状态转移，这样会对最终的答案产生影响吗？这个问题留给读者进行思考。这里我认为是不影响的，题目是解法的一个特例。