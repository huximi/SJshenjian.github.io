---
title: 16_最接近的三数之和_leetcode
date: 2019-09-16 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 16. 最接近的三数之和

给定一个包括 n 个整数的数组 nums 和 一个目标值 target。找出 nums 中的三个整数，使得它们的和与 target 最接近。返回这三个数的和。假定每组输入只存在唯一答案。

>例如，给定数组 nums = [-1，2，1，-4], 和 target = 1.
与 target 最接近的三个数的和为 2. (-1 + 2 + 1 = 2).

题解： 排序+双指针(leetcode15三数之和也是用该思想奥)

```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
         if (nums == null || nums.length < 3) {
             return 0;
         }
         Arrays.sort(nums);
         int result = nums[0] + nums[1] + nums[2];
         for (int i = 0; i < nums.length; i++) {
             int value = nums[i];
             int left = i + 1;
             int right = nums.length - 1;
             while (left < right) {
                 int threeSum = value + nums[left]+ nums[right];
                 // 更新
                 if (Math.abs(threeSum - target) < Math.abs(result - target)) {
                     result = threeSum;
                 }
                 // 指针移动向目标值靠拢，大则right--，小则left++
                 if (threeSum > target) {
                     right--;
                 } else if (threeSum < target) {
                     left++;
                 } else {
                     return result;
                 }
             }
         }
         return result;
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/3sum-closest/)