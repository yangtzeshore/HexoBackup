---
title: TextJustification
date: 2021-04-29 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 文本左右对齐

- [题目介绍](https://yangtzeshore.github.io/2021/04/29/TextJustification/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/04/29/TextJustification/#题目解法)

### 题目介绍

#### [文本左右对齐](https://leetcode-cn.com/problems/text-justification/)

给定一个单词数组和一个长度 *maxWidth*，重新排版单词，使其成为每行恰好有 *maxWidth* 个字符，且左右两端对齐的文本。

你应该使用“贪心算法”来放置给定的单词；也就是说，尽可能多地往每行中放置单词。必要时可用空格 `' '` 填充，使得每行恰好有 *maxWidth* 个字符。

要求尽可能均匀分配单词间的空格数量。如果某一行单词间的空格不能均匀分配，则左侧放置的空格数要多于右侧的空格数。

文本的最后一行应为左对齐，且单词之间不插入**额外的**空格。

**说明:**

- 单词是指由非空格字符组成的字符序列。
- 每个单词的长度大于 0，小于等于 *maxWidth*。
- 输入单词数组 `words` 至少包含一个单词。

**示例1：**

```
输入:
words = ["This", "is", "an", "example", "of", "text", "justification."]
maxWidth = 16
输出:
[
   "This    is    an",
   "example  of text",
   "justification.  "
]
```

**示例2：**

```
输入:
words = ["What","must","be","acknowledgment","shall","be"]
maxWidth = 16
输出:
[
  "What   must   be",
  "acknowledgment  ",
  "shall be        "
]
解释: 注意最后一行的格式应为 "shall be    " 而不是 "shall     be",
     因为最后一行应为左对齐，而不是左右两端对齐。       
     第二行同样为左对齐，这是因为这行只包含一个单词。
```

**示例3：**

```
输入:
words = ["Science","is","what","we","understand","well","enough","to","explain",
         "to","a","computer.","Art","is","everything","else","we","do"]
maxWidth = 20
输出:
[
  "Science  is  what we",
  "understand      well",
  "enough to explain to",
  "a  computer.  Art is",
  "everything  else  we",
  "do                  "
]
```

### 题目解法

```
package algorithm;

import java.util.ArrayList;
import java.util.List;

/**
 * 1. 先取出一行能够容纳的单词，将这些单词根据规则填入一行
 *
 * 2. 计算出额外空格的数量 spaceCount，额外空格就是正常书写用不到的空格
 * 2.1. 由总长度算起
 * 2.2. 除去每个单词末尾必须的空格，为了统一处理可以在结尾虚拟加上一个长度
 * 2.3. 除去所有单词的长度
 *
 * 3. 按照单词的间隙数量 wordCount - 1，简单来说就是商和余数的计算
 * 3.1 对于每个词填充之后，需要填充的空格数量等于 spaceSuffix + spaceAvg + ((i - bg) < spaceExtra)
 * spaceSuffix 【单词尾部固定的空格，就是1】
 * spaceAvg 【额外空格的平均值，每个间隙都要填入 spaceAvg 个空格】
 * ((i - bg) < spaceExtra) 【额外空格的余数，前 spaceExtra 个间隙需要多 1 个空格】，这个是补偿给前面的间隙的
 *
 * 4. 特殊处理
 * 4.1. 一行只有一个单词，单词左对齐，右侧填满空格
 * 4.2. 最后一行，所有单词左对齐，中间只有一个空格，最后一个单词右侧填满空格
 *
 */
public class TextJustification {

    // 行整理函数
    private static String fillWords(String[] words, int startIndex, int endIndex, int maxWidth, boolean lastLine) {
        int wordCount = endIndex - startIndex + 1;
        // 除去每个单词尾部空格， + 1 是最后一个单词的尾部空格的特殊处理，就是实际减去间隙的数量
        int spaceCount = maxWidth + 1 - wordCount;
        for (int i = startIndex; i <= endIndex; i++) {
            // 除去所有单词的长度
            spaceCount -= words[i].length();
        }

        // 词尾空格
        int spaceSuffix = 1;
        // 额外空格的平均值
        int spaceAvg = (wordCount == 1) ? 1 : spaceCount / (wordCount - 1);
        // 额外空格的余数
        int spaceExtra = (wordCount == 1) ? 0 : spaceCount % (wordCount - 1);

        StringBuilder ans = new StringBuilder();
        for (int i = startIndex; i < endIndex; i++) {
            // 填入单词
            ans.append(words[i]);
            // 特殊处理最后一行，需要左对齐且空格一个
            if (lastLine) {
                ans.append(' ');
                continue;
            }
            // 根据计算结果补上空格
            int temp = spaceSuffix + spaceAvg;
            if ((i - startIndex) < spaceExtra) {
                temp += 1;
            }
            while (temp-- > 0) {
                ans.append(' ');
            }
        }
        // 填入最后一个单词
        ans.append(words[endIndex]);
        // 补上这一行最后的空格
        int temp = maxWidth - ans.length();
        while (temp-- > 0) {
            ans.append(' ');
        }
        return ans.toString();
    }

    public static List<String> fullJustify(String[] words, int maxWidth) {
        List<String> ans = new ArrayList<>();
        int lineLength = 0;
        int startIndex = 0;
        for (int i = 0; i < words.length; i++) {
            lineLength += words[i].length() + 1;

            // 如果是最后一个单词，或者加上下一个词就超过长度了，即可凑成一行
            if (i + 1 == words.length || lineLength + words[i + 1].length() > maxWidth) {
                ans.add(fillWords(words, startIndex, i, maxWidth, i + 1 == words.length));
                startIndex = i + 1;
                lineLength = 0;
            }
        }
        return ans;
    }

    public static void main(String[] args) {
		String[] words = new String[]{"This", "is", "an", "example", "of", "text", "justification."};
        int maxWidth = 16;
        System.out.println(fullJustify(words, maxWidth));

        words = new String[]{"What", "must", "be", "acknowledgment", "shall", "be"};
        maxWidth = 16;
        System.out.println(fullJustify(words, maxWidth));

        words = new String[]{"Science", "is", "what", "we", "understand", "well", "enough", "to", "explain",
                "to", "a", "computer.", "Art", "is", "everything", "else", "we", "do"};
        maxWidth = 20;
        System.out.println(fullJustify(words, maxWidth));

//        String[] words = new String[]{"What", "must", "bexx", "a"};
//        int maxWidth = 16;
//        System.out.println(fullJustify(words, maxWidth));
    }
}
```

打印：

```
[This    is    an, example  of text, justification.  ]
[What   must   be, acknowledgment  , shall be        ]
[Science  is  what we, understand      well, enough to explain to, a  computer.  Art is, everything  else  we, do    
```

**思路：**

参考了其他的答主，主要是将空间拆开成三部分，很巧妙，可以参考下图。