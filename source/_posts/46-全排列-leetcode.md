---
title: 46_全排列_leetcode
date: 2019-12-28 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 46. 全排列

给定一个没有重复数字的序列，返回其所有可能的全排列。

>示例:
输入: [1,2,3]
输出:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]


**题解：** 回溯算法，Set用于判断数字是否已经用于排列，Stack先进后出特性 + 递归实现回溯

```java
public class Solution {
    public List<List<Integer>> permute(int[] nums) {
        int len = nums.length;
        List<List<Integer>> res = new ArrayList<>();
        if (len == 0) {
            return res;
        }

        Set<Integer> used = new HashSet<>();
        Stack<Integer> stack = new Stack<>();
        backtrack(nums, 0, len, used, stack, res);
        return res;
    }

    private void backtrack(int[] nums, int depth, int len, Set<Integer> used, Stack<Integer> stack, List<List<Integer>> res) {
        if (depth == nums.length) { // 全部数字已排列完，加入结果集
            res.add(new ArrayList<>(stack));
            return ;
        }
        for (int i = 0; i < len; i++) {
            if (!used.contains(i)) { // 排列之前未排列的数字
                used.add(i);
                stack.push(nums[i]);
                // 递归求解
                backtrack(nums, depth + 1, len, used, stack, res);
                stack.pop();
                used.remove(i);
            }
        }
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/permutations/)