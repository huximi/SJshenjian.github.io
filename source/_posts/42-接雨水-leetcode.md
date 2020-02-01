---
title: 42_接雨水_leetcode
date: 2019-12-13 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 42. 接雨水

给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。(该题出现过字节跳动的笔试题中)

![雨水](雨水.png)

>输入: [0,1,0,2,1,0,1,3,2,1,2,1]
输出: 6

**题解：** 双指针求解法，左右索引向中间靠拢，记录当前左右最高高度，
若左索引小于左最高点，则可积水，更新积水面积；
若左索引大于等于左最高点，则不可积水，更新左最高点，
右索引同理

```java
class Solution {
    public int trap(int[] height) {
        int leftHeightMax = 0, rightHeightMax = 0;
        int left = 0, right = height.length - 1;
        int ans = 0;

        while (left < right) {
            if (height[left] < height[right]) {
                if (height[left] >= leftHeightMax) {
                    leftHeightMax = height[left];
                } else {
                    ans += leftHeightMax - height[left];
                }
                left++;
            } else {
                if (height[right] >= rightHeightMax) {
                    rightHeightMax = height[right];
                } else {
                    ans += rightHeightMax - height[right];
                }
                right--;
            }
        }
        return ans;
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/trapping-rain-water/)