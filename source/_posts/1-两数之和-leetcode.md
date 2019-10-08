---
title: 1_两数之和_leetcode
date: 2019-08-11 21:30:24
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 1. 两数之和

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

>示例:
给定 nums = [2, 7, 11, 15], target = 9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]

题解：

```java
class Solution {

    /**
     * 以nums元素为key,下标为value，
     * 若遍历时存在target - nums[i]的key, 则找到输出即可
     */
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            if (map.containsKey(target - nums[i])) {
                return new int[]{map.get(target - nums[i]), i};
            }
            map.put(nums[i], i);
        }
        throw new IllegalArgumentException("No two sum solution");
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/two-sum/)