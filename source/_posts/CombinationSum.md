---
title: CombinationSum
date: 2021-02-20 09:22:21
tags:
- 
categories:
- Leetcode 
---



#### 组合总和

- [题目介绍](https://yangtzeshore.github.io/2021/02/20/CombinationSum/#题目介绍)
- [题目解法](https://yangtzeshore.github.io/2021/02/20/CombinationSum/#题目解法)

### 题目介绍

#### [组合总和](https://leetcode-cn.com/problems/combination-sum/)

给定一个**无重复元素**的数组 `candidates` 和一个目标数 `target` ，找出 `candidates` 中所有可以使数字和为 `target` 的组合。

`candidates` 中的数字可以无限制重复被选取。

**说明：**

- 所有数字（包括 `target`）都是正整数。
- 解集不能包含重复的组合。

**示例 1：**

```
输入：candidates = [2,3,6,7], target = 7,
所求解集为：
[
  [7],
  [2,2,3]
]
```

**示例 2：**

```
输入：candidates = [2,3,5], target = 8,
所求解集为：
[
  [2,2,2,2],
  [2,3,3],
  [3,5]
]
```

**提示：**

- `1 <= candidates.length <= 30`
- `1 <= candidates[i] <= 200`
- `candidate` 中的每个元素都是独一无二的。
- `1 <= target <= 500`

### 题目解法

```
package algorithm;

import java.util.ArrayList;
import java.util.List;

public class CombinationSum {

    public static List<List<Integer>> combinationSum(int[] candidates, int target) {

        List<List<Integer>> result = new ArrayList<>();
        List<Integer> element = new ArrayList<>();
        dfs(result, element, candidates, target, 0);
        return result;
    }

    private static void dfs(List<List<Integer>> result, List<Integer> element, int[] candidates, int target, int index) {

        if (target == 0) {
            // 注意因为会回溯，所以需要新建一个，回溯会改变集合
            result.add(new ArrayList<>(element));
            return;
        }
        if (index == candidates.length) {
            return;
        }
        // 跳过index
        dfs(result, element, candidates, target, index + 1);
        // 使用当前index
        if (target - candidates[index] >= 0) {
            element.add(candidates[index]);
            dfs(result, element, candidates, target - candidates[index], index);
            element.remove(element.size() - 1);
        }
    }

    public static void main(String[] args) {

        int[] candidates = new int[]{2,3,6,7};
        int targe = 7;
        System.out.println(combinationSum(candidates, targe));

        candidates = new int[]{2,3,5};
        targe = 8;
        System.out.println(combinationSum(candidates, targe));
    }
}
```

打印：

```
[[7], [2, 2, 3]]
[[3, 5], [2, 3, 3], [2, 2, 2, 2]]
```

**思路：**

思路也很简单，就是搜索回溯。