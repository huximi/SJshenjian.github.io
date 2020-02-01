---
title: 47_全排列II_leetcode
date: 2019-12-29 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 47. 全排列II

给定一个可包含重复数字的序列，返回所有不重复的全排列

>示例:
输入: [1,1,2]
输出:
[
  [1,1,2],
  [1,2,1],
  [2,1,1]
]


**题解：** 排序+回溯算法+剪枝，Set用于判断数字是否已经用于排列，Stack先进后出特性 + 递归实现回溯

```java
public class Solution {
    public List<List<Integer>> permuteUnique(int[] nums) {
        int len = nums.length;
        List<List<Integer>> res = new ArrayList<>();
        if (len == 0) {
            return res;
        }

        Set<Integer> used = new HashSet<>();
        Stack<Integer> stack = new Stack<>();
        // 首先排序，之后才有可能发现重复分支
        Arrays.sort(nums);
        backtrack(nums, 0, len, used, stack, res);
        return res;
    }

    private void backtrack(int[] nums, int depth, int len, Set<Integer> used, Stack<Integer> stack, List<List<Integer>> res) {
        if (depth == nums.length) {
            res.add(new ArrayList<>(stack));
            return ;
        }
        for (int i = 0; i < len; i++) {
        	// 剪枝： 和之前的数相等，并且之前的数没有使用过，才会出现相同分支
        	// 如 1 2 2 2, 去除相同的1 2
            if (i > 0 && nums[i] == nums[i -1] && !used.contains(i - 1)) {
                continue;
            }
            if (!used.contains(i)) {
                used.add(i);
                stack.push(nums[i]);
                backtrack(nums, depth + 1, len, used, stack, res);
                stack.pop();
                used.remove(i);
            }
        }
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/permutations-ii/)