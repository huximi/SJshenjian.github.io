---
title: 45_跳跃游戏II_leetcode
date: 2019-12-19 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 45. 跳跃游戏II

给定一个非负整数数组，你最初位于数组的第一个位置。
数组中的每个元素代表你在该位置可以跳跃的最大长度。
你的目标是使用最少的跳跃次数到达数组的最后一个位置。

>输入: [2,3,1,1,4]
输出: 2
解释: 跳到最后一个位置的最小跳跃数是 2。
     从下标为 0 跳到下标为 1 的位置，跳 1 步，然后跳 3 步到达数组的最后一个位置。
说明:
假设你总是可以到达数组的最后一个位置。

**题解：** 贪心算法 每次在可跳范围内选择可以使得跳的更远的位置

```java
class Solution {
    public int jump(int[] nums) {
        int end = 0;
        int maxPosition = 0;
        int steps = 0;

        //  for 循环中，i < nums.length - 1，少了末尾。
        //  因为开始的时候边界是第0个位置，steps已经加1了。
        //  如果最后一步刚好跳到了末尾，此时steps其实不用加1了。
        //  如果是i < nums.length，i遍历到最后的时候，会进入if语句中，steps会多加1。
        for (int i = 0; i < nums.length - 1; i++) {
            maxPosition = Math.max(maxPosition, nums[i] + i);
            if (i == end) {
                end = maxPosition;
                steps++;
            }
        }
        return steps;
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/jump-game-ii/)