---
title: 35_搜索插入位置_leetcode
date: 2019-11-19 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 35. 搜索插入位置

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。
你可以假设数组中无重复元素。

>输入: [1,3,5,6], 5
输出: 2
输入: [1,3,5,6], 2
输出: 1
输入: [1,3,5,6], 7
输出: 4
输入: [1,3,5,6], 0
输出: 0


**题解：** 二分查找。注意模板即可，right=nums.length,则right=mid即可， 若right=nums.length-1,则right=mid-1

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        if (nums == null || nums.length <= 0) {
            return 0;
        }
        int left = 0, right = nums.length;
        while (left < right) {
            // 考虑到内存溢出，该方式安全些
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {
                return mid;
            }
            if (nums[mid] > target) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/search-insert-position/)