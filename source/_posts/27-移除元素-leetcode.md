---
title: 27_移除元素_leetcode
date: 2019-10-13 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 27. 移除元素

给定一个数组 nums 和一个值 val，你需要原地移除所有数值等于 val 的元素，返回移除后数组的新长度。
不要使用额外的数组空间，你必须在原地修改输入数组并在使用 O(1) 额外空间的条件下完成。
元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

>给定 nums = [3,2,2,3], val = 3,
函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。
你不需要考虑数组中超出新长度后面的元素。

给定 nums = [0,1,2,2,3,0,4,2], val = 2,
函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。
注意这五个元素可为任意顺序。
你不需要考虑数组中超出新长度后面的元素。

题解： 简单题，给AC加点心

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        if (nums == null) {
        	return 0;
        }
        int length = 0;
        for (int i = 0; i < nums.length; i++) {
        	if (nums[i] != val) {
        		nums[length++] = nums[i];
        	}
        }
        return length;
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/remove-element/)