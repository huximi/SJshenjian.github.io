---
title: 40_组合总和II_leetcode
date: 2019-12-11 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 40. 组合总和II

给定一个数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。
candidates 中的每个数字在每个组合中只能使用一次
说明：所有数字（包括目标数）都是正整数。解集不能包含重复的组合。 

>输入: candidates = [10,1,2,7,6,1,5], target = 8,
所求解集为:
[
  [1, 7],
  [1, 2, 5],
  [2, 6],
  [1, 1, 6]
]

**题解：** 回溯算法+剪枝，关键在于栈与递归巧妙应用

```java
/**
 * 回溯 + 剪枝
 */
public class Solution {

    private List<List<Integer>> res = new ArrayList<>();
    private int[] candidates;
    private int len;

    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        if (candidates == null || candidates.length <= 0) {
            return res;
        }

        Arrays.sort(candidates);
        this.len = candidates.length;
        this.candidates = candidates;
        findCombinationSum2(target, 0, new Stack<>());
        return res;
    }

    private void findCombinationSum2(int residue, int start, Stack<Integer> pre) {
        if (residue == 0) { // 进行结算，即找到解
            res.add(new ArrayList<>(pre));
        }
        // residue - candidates[i] >= 0 剪枝
        for (int i = start; i < len && residue - candidates[i] >= 0; i++) {
            // 相同部分剪枝(与题目39组合总和主要不同之处)
            if (i > start && candidates[i] == candidates[i - 1]) {
                continue;
            }

            pre.add(candidates[i]);
            findCombinationSum2(residue - candidates[i], i + 1, pre);
            pre.pop();
        }
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/combination-sum-ii/)