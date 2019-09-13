---
title: 11_盛最多水的容器_leetcode
date: 2019-09-03 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 11. 盛最多水的容器

给定 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

说明：你不能倾斜容器，且 n 的值至少为 2

示例:

输入: [1,8,6,2,5,4,8,3,7]
输出: 49

```java
/**
 * 双指针法
 * 宽度一定时，木桶容量受限于最左最右高度，故取0与height.length-1为最左最右开始(保证了宽度初始化最大)
 */
class Solution {

    public int maxArea(int[] height) {
        int max = 0;
        int left = 0;
        int right = height.length - 1;

        while (left < right) {
            int capacity = (right - left) * Math.min(height[left], height[right]);
            if (capacity > max) {
                max = capacity;
            }

            // 移动短的一端，虽然宽度减小，但可能新的高度使得容量最大
            // 若移动长的一端，木桶容量仍然受限于最短端，且宽度减小，故容量不可能出现更大者
            if (height[left] < height[right]) {
                left++;
            } else {
                right--;
            }
        }
        return max;
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/container-with-most-water/)