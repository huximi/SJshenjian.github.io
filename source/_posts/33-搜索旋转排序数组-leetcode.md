---
title: 33_搜索旋转排序数组_leetcode
date: 2019-11-02 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 33. 搜索旋转排序数组

假设按照升序排序的数组在预先未知的某个点上进行了旋转。
( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。
搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。
你可以假设数组中不存在重复的元素。
你的算法时间复杂度必须是 O(log n) 级别。

>输入: nums = [4,5,6,7,0,1,2], target = 0
输出: 4
输入: nums = [4,5,6,7,0,1,2], target = 3
输出: -1


**题解：** 时间复杂度O(log n),我们可以想到二分查找，由于存在旋转点，故二分点要么在旋转点左侧，要么在其右侧(这里的右侧假定包含旋转点)，则
可分为两种情况讨论：
1）在左侧即0~mid有序(因为不含有旋转点)
2）在右侧即0~mid无序(因为包含旋转点)
找出这两种情况low=mid+1的条件即可，详见代码

```java
class Solution {
	public int search(int[] nums, int target) {
		int low = 0;
		int high = nums.length - 1;
		while (low < high) {
			int mid = (low + high) / 2;
			if (nums[mid] == target) {
				return mid;
			}
			// 把数组大致分为两组，一组为左侧未旋转有序数组，一组为右侧旋转有序数组
			// 如[3 4 5 1 2]， [3，4，5]称为左侧，[1，2]称为右侧
			
			// 0~mid有序，向后规约条件
			// nums[mid] >= nums[0] 表示0~mid有序
			// target > nums[mid] 表示target位于左侧且大于nums[mid],向后规约
			// target < nums[0] 表示target位于右侧，向后规约
			if (nums[mid] >= nums[0] && (target > nums[mid] || target < nums[0])) {
				low = mid + 1;
			} else if (nums[mid] < nums[0] && target > nums[mid] && target < nums[0]) { // 0~mid无序(即包含翻转点)，向后规约条件
				// nums[mid] < nums[0] 表示nums[mid]位于右侧
				low = mid + 1;
			} else {
				high = mid - 1;
			}
		}
		return low == high && nums[low] == target ? low : -1;
	}
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)