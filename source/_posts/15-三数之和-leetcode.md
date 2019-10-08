---
title: 15_三数之和_leetcode
date: 2019-09-13 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 15. 三数之和

给定一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？找出所有满足条件且不重复的三元组。

注意：答案中不可以包含重复的三元组。

>例如, 给定数组 nums = [-1, 0, 1, 2, -1, -4]，
满足要求的三元组集合为：
[
  [-1, 0, 1],
  [-1, -1, 2]
]

题解： 排序+双指针(截至目前，leetcode11盛最多水的容器也采用双针法)

```java
class Solution {
	public static List<List<Integer>> threeSum(int[] nums) {
		List<List<Integer>> result = new ArrayList<>();
		if (nums == null || nums.length < 3) {
			return result;
		}
		Arrays.sort(nums);
		for (int i = 0; i < nums.length; i++) {
			if (nums[i] > 0) { // 有序的数组中第一个数大于0,则不存在三数之和等于0，结束循环
				break;
			}
			// 去重，如-7 -7 3 4 4 4
			if (i > 0 && nums[i] == nums[i - 1]) {
				continue;
			}
			int current = nums[i];
			int left = i + 1; // 左指针
			int right = nums.length - 1; // 右指针
			while (left < right) {
				int sum = current + nums[left] + nums[right];
				if (sum == 0) {
					result.add(Arrays.asList(current, nums[left], nums[right]));
					// 去重，如 -7 3 4 4 4  ,结果仅为 -7,3,4
					while (left < right && nums[left] == nums[left + 1]) {
						left++;
					}
					while (left < right && nums[right] == nums[right - 1]) {
						right--;
					}
					left++;
					right--;
				} else if (sum > 0) {
					// 和过大则右指针较小(右指针处值较大)，反之，增大左指针
					right--;
				} else {
					left++;
				}
			}
		}
		return result;
	}
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/3sum/submissions/)