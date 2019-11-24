---
title: 34_在排序数组中查找元素的第一个和最后一个位置_leetcode
date: 2019-11-13 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 34. 在排序数组中查找元素的第一个和最后一个位置

给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置。
你的算法时间复杂度必须是 O(log n) 级别。
如果数组中不存在目标值，返回 [-1, -1]。

>输入: nums = [5,7,7,8,8,10], target = 8
输出: [3,4]
输入: nums = [5,7,7,8,8,10], target = 6
输出: [-1,-1]


**题解：** 数组有序且时间复杂度O(log n),我们可以想到二分查找。关键在于二分搜索到值后不能停止搜索，则应该继续向左或向右搜索，以便找到第一个和最后一个位置

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int[] res = {-1, -1};
        int left = 0, right = nums.length;
        left = findIndex(nums, target, true); // 向左查找
        if (left == nums.length || nums[left] != target) {
            return res;
        }

        res[0] = left;
        res[1] = findIndex(nums, target, false) - 1; // 向右查找
        return res;
    }

    private int findIndex(int[] nums, int target, boolean isLeftSearch) {
        int left = 0, right = nums.length;
        while (left < right) {
            int mid = (left + right) / 2;
            // 官方题解，这里巧妙的控制了向左向右查找
            if (nums[mid] > target || (isLeftSearch && nums[mid] == target)) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)