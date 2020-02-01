---
title: 39_组合总和_leetcode
date: 2019-12-10 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 39. 组合总和

给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。
candidates 中的数字可以无限制重复被选取。
说明：所有数字（包括 target）都是正整数。解集不能包含重复的组合。 

>输入: candidates = [2,3,6,7], target = 7,
所求解集为:
[
  [7],
  [2,2,3]
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

    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        if (candidates == null || candidates.length <= 0) {
            return res;
        }

        Arrays.sort(candidates);
        this.len = candidates.length;
        this.candidates = candidates;
        findCombinationSum(target, 0, new Stack<>());
        return res;
    }

    private void findCombinationSum(int residue, int start, Stack<Integer> pre) {
        if (residue == 0) { // 进行结算，即找到解
            res.add(new ArrayList<>(pre));
        }
        // residue - candidates[i] >= 0 剪枝小于0的解
        for (int i = start; i < len && residue - candidates[i] >= 0; i++) {
            pre.add(candidates[i]);
            // 因为candidates 中的数字可以无限制重复被选取，故仍然取i
            findCombinationSum(residue - candidates[i], i, pre);
            pre.pop();
        }
    }
}

```

AC传送门[leetcode](https://leetcode-cn.com/problems/combination-sum/)