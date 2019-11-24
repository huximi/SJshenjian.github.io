---
title: 31_下一个排列_leetcode
date: 2019-10-22 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 31. 下一个排列

实现获取下一个排列的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。
如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。
必须原地修改，只允许使用额外常数空间。

>以下是一些例子，输入位于左侧列，其相应输出位于右侧列。
1,2,3 → 1,3,2
3,2,1 → 1,2,3
1,1,5 → 1,5,1

**题解：** 1. 从右到左，找打第一个非降序的值，为待交换目标
2. 从右到左，找到第一个大于待交换目标的值，作为交换值
3. 交换
4. 在1中位置处后的数据必定降序，故反转，从而得到下一个排列

```java
public class Solution {

	public void nextPermutation(int[] nums) {
		// 1 3 2 1
		// 1. 从右到左，找打第一个非降序的值，为待交换目标
		int i = nums.length - 2;
		while (i >= 0 && nums[i + 1] <= nums[i]) {
			i--;
		}
		
		// 2.从右到左，找到第一个大于待交换目标的值，作为交换值
		if (i >= 0) {
			int j = nums.length - 1;
			while (j >= 0 && nums[j] <= nums[i]) {
				j--;
			}
			// 3.交换
			swap(nums, i, j);
		}
		// 4.nums[i]后数据必定降序，故反转为正序
		// 在1中nums[i]之后为降序，在2中交换值后，也为降序[自己简单逻辑思考下奥]
		reverse(nums, i + 1);
	}
	
	private void reverse(int[] nums, int start) {
		int i = start, j = nums.length - 1;
		while (i < j) {
			swap(nums, i, j);
			i++;
			j--;
		}
	}
	
	private void swap(int[] nums, int i, int j) {
		int temp = nums[i];
		nums[i] = nums[j];
		nums[j] = temp;
	}
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/next-permutation/)