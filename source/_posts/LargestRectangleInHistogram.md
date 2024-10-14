---
title: LargestRectangleInHistogram
date: 2021-06-05 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 柱状图中最大的矩形

- [题目介绍](https://yangtzeshore.github.io/2021/06/05/LargestRectangleInHistogram/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/06/05/LargestRectangleInHistogram/#题目解法)

### 题目介绍

#### [柱状图中最大的矩形](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)

给定 *n* 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。

求在该柱状图中，能够勾勒出来的矩形的最大面积。

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/histogram.png)

以上是柱状图的示例，其中每个柱子的宽度为 1，给定的高度为 `[2,1,5,6,2,3]`。

![img](https://raw.githubusercontent.com/yangtzeshore/images/main/Leetcode/histogram_area.png)

图中阴影部分为所能勾勒出的最大矩形面积，其面积为 `10` 个单位。

**示例：**

```
输入: [2,1,5,6,2,3]
输出: 10
```

### 题目解法

```
package algorithm;

import java.util.Stack;

public class LargestRectangleInHistogram {

    public static int largestRectangleArea(int[] heights) {
        Stack<Integer> stack = new Stack<>();
        int length = heights.length;
        int[] left = new int[length];
        int[] right = new int[length];

        for (int i = 0; i < length; i++) {
            while (!stack.isEmpty() && heights[stack.peek()] >= heights[i]) {
                stack.pop();
            }
            left[i] = stack.isEmpty() ? -1 : stack.peek();
            stack.push(i);
        }

        stack.clear();
        for (int i = length - 1; i >= 0; i--) {
            while (!stack.isEmpty() && heights[stack.peek()] >= heights[i]) {
                stack.pop();
            }
            right[i] = stack.isEmpty() ? length : stack.peek();
            stack.push(i);
        }

        int ans = 0;
        for (int i = 0; i < length; i++) {
            ans = Math.max(ans, (right[i] - left[i] - 1) * heights[i]);
        }

        return ans;
    }

    public static void main(String[] args) {

        int[] heights= new int[]{2, 1, 5, 5, 2, 3};
        System.out.println(largestRectangleArea(heights));
    }
}
```

打印：

```
10
```

**思路：**

如果只是求解，倒是不难，双指针枚举，不过效率太低，可能过不了，所以采用了单调栈的方法。思路也是异常的简单和巧妙，就是遍历过程中，记录每个值左边和右边的边界。主要是每次比较的过程中，上一个已经帮我们筛选了一次，这一次，只需要比较自己和上一个值，以及上一个值的边界即可。详细的解释，可以参考其他答案，官网的解释有点费解。