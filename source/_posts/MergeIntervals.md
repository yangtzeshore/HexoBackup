---
title: MergeIntervals
date: 2021-03-29 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 合并区间

- [题目介绍](https://yangtzeshore.github.io/2021/03/29/MergeIntervals/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/03/29/MergeIntervals/#题目解法)

### 题目介绍

#### [合并区间](https://leetcode-cn.com/problems/merge-intervals/)

以数组 `intervals` 表示若干个区间的集合，其中单个区间为 intervals[i]=[starti,endi] 。请你合并所有重叠的区间，并返回一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间。

**示例1：**

```
输入：intervals = [[1,3],[2,6],[8,10],[15,18]]
输出：[[1,6],[8,10],[15,18]]
解释：区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
```

**示例2：**

```
输入：intervals = [[1,4],[4,5]]
输出：[[1,5]]
解释：区间 [1,4] 和 [4,5] 可被视为重叠区间。
```

**提示：**

- 1<=intervals.length<=104
- `intervals[i].length == 2`
- 0<=starti<=endi<=104

### 题目解法

```
package algorithm;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class MergeIntervals {

    public static int[][] merge(int[][] intervals) {

        // 排序+合并
        Arrays.sort(intervals, (interval1, interval2) -> interval1[0] - interval2[0]);

        List<int[]> ans = new ArrayList<>();
        for (int i = 0; i < intervals.length; i++) {
            int left = intervals[i][0];
            int right = intervals[i][1];
            if (ans.size() == 0 || ans.get(ans.size() - 1)[1] < left) {
                ans.add(intervals[i]);
            } else {
                ans.get(ans.size() - 1)[1] = Math.max(ans.get(ans.size() - 1)[1], right);
            }
        }
        return ans.toArray(new int[ans.size()][]);
    }

    public static void main(String[] args) {
        int[][] intervals = new int[][] {{1,3},{2,6},{8,10},{15,18}};
        print(merge(intervals));

        intervals = new int[][] {{1,4},{4,5}};
        print(merge(intervals));
    }

    private static void print(int[][] intervals) {
        for (int[] interval : intervals) {
            System.out.print("[");
            System.out.print(interval[0]);
            System.out.print(",");
            System.out.print(interval[1]);
            System.out.print("]");
        }
        System.out.print(", ");
    }
}
```

打印：

```
[1,6][8,10][15,18], [1,5], 
```

**思路：**

思路上，就是排序和比较，不过效率很低。后续有时间，写一个效率好一点的算法。