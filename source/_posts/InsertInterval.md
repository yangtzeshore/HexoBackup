---
title: InsertInterval
date: 2021-04-01 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 插入区间

- [题目介绍](https://yangtzeshore.github.io/2021/04/01/InsertInterval/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/04/01/InsertInterval/#题目解法)

### 题目介绍

#### [插入区间](https://leetcode-cn.com/problems/insert-interval/)

给你一个 **无重叠的** *，*按照区间起始端点排序的区间列表。

在列表中插入一个新的区间，你需要确保列表中的区间仍然有序且不重叠（如果有必要的话，可以合并区间）。

**示例1：**

```
输入：intervals = [[1,3],[6,9]], newInterval = [2,5]
输出：[[1,5],[6,9]]
```

**示例2：**

```
输入：intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
输出：[[1,2],[3,10],[12,16]]
解释：这是因为新的区间 [4,8] 与 [3,5],[6,7],[8,10] 重叠。
```

**示例3：**

```
输入：intervals = [], newInterval = [5,7]
输出：[[5,7]]
```

**示例4：**

```
输入：intervals = [[1,5]], newInterval = [2,3]
输出：[[1,5]]
```

**示例5：**

```
输入：intervals = [[1,5]], newInterval = [2,7]
输出：[[1,7]]
```

**提示：**

- 0<=intervals.length<=104
- `intervals[i].length == 2`
- 0<=intervals[i][0]<=intervals[i][1]<=105
- `intervals` 根据 `intervals[i][0]` 按 **升序** 排列
- `newInterval.length == 2`
- 0<=newInterval[0]<=newInterval[1]<=105

### 题目解法

```
package algorithm;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class InsertInterval {

    public static int[][] insert(int[][] intervals, int[] newInterval) {

        if (intervals == null || intervals.length == 0 || intervals[0].length == 0) {
            return new int[][]{{newInterval[0], newInterval[1]}};
        }

        int left = newInterval[0];
        int right = newInterval[1];
        // 1 插入
        // 2 合并，包括跨一个，和多个
        // 想象成游标右移
        List<int[]> ans = new ArrayList<>();
        int i = 0;
        for (; i < intervals.length; i++) {
            if (intervals[i][1] < left) {
                ans.add(new int[]{intervals[i][0], intervals[i][1]});
                if (i == intervals.length - 1) {
                    ans.add(new int[]{left, right});
                }
            } else if (intervals[i][0] > right) {
                ans.add(new int[]{left, right});
                ans.add(new int[]{intervals[i][0], intervals[i][1]});
                break;
            } else if (intervals[i][1] >= right) {
                ans.add(new int[]{Math.min(left, intervals[i][0]), intervals[i][1]});
                break;
            } else {
                left = Math.min(left, intervals[i][0]);
                i++;
                while (i < intervals.length && right >= intervals[i][0]) {
                    i++;
                }
                i--;
                right = Math.max(right, intervals[i][1]);
                ans.add(new int[]{left, right});
                break;
            }
        }

        while (++i < intervals.length) {
            ans.add(new int[]{intervals[i][0], intervals[i][1]});
        }

        return ans.toArray(new int[ans.size()][]);
    }

    public static void main(String[] args) {
        // intervals = [[1,3],[6,9]], newInterval = [2,5]
        int[][] intervals = new int[][]{{1, 3}, {6, 9}};
        int[] newInterval = new int[]{2, 5};
        System.out.println(Arrays.deepToString(insert(intervals, newInterval)));

        // intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
        intervals = new int[][]{{1, 2}, {3, 5}, {6, 7}, {8, 10}, {12, 16}};
        newInterval = new int[]{4, 8};
        System.out.println(Arrays.deepToString(insert(intervals, newInterval)));

        // intervals = [], newInterval = [5,7]
        intervals = new int[][]{{}};
        newInterval = new int[]{5, 7};
        System.out.println(Arrays.deepToString(insert(intervals, newInterval)));

        // intervals = [[1,5]], newInterval = [2,3]
        intervals = new int[][]{{1, 5}};
        newInterval = new int[]{2, 3};
        System.out.println(Arrays.deepToString(insert(intervals, newInterval)));

        // intervals = [[1,5]], newInterval = [2,7]
        intervals = new int[][]{{1, 5}};
        newInterval = new int[]{2, 7};
        System.out.println(Arrays.deepToString(insert(intervals, newInterval)));

        intervals = new int[][]{{1, 5}};
        newInterval = new int[]{6, 8};
        System.out.println(Arrays.deepToString(insert(intervals, newInterval)));

    }
}
```

打印：

```
[[1, 5], [6, 9]]
[[1, 2], [3, 10], [12, 16]]
[[5, 7]]
[[1, 5]]
[[1, 7]]
```

**思路：**

思路上，可以将插入的值作为游标滑动在原数组上。