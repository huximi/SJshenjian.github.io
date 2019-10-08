---
title: 18_四数之和_leetcode
date: 2019-09-21 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 18. 四数之和

给定一个包含 n 个整数的数组 nums 和一个目标值 target，判断 nums 中是否存在四个元素 a，b，c 和 d ，使得 a + b + c + d 的值与 target 相等？找出所有满足条件且不重复的四元组。

注意：
答案中不可以包含重复的四元组。

>示例：
给定数组 nums = [1, 0, -1, 0, -2, 2]，和 target = 0。
满足要求的四元组集合为：
[
  [-1,  0, 0, 1],
  [-2, -1, 1, 2],
  [-2,  0, 0, 2]
]

题解： 指针 + 双指针法 (四数之和转换为三数之和求解)

```java
class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
    	List<List<Integer>> result = new ArrayList<>();
    	if (nums == null || nums.length < 4) {
    		return result;
    	}
    	
    	Arrays.sort(nums);
    	for (int i = 0; i < nums.length; i++) { 
    		if (isDuplicate(i, 0, nums)) { // 去重  
    			continue;
    		}
    		// 以下转换为3数求和
    		threeSum(nums, i, target - nums[i], result);
    	}
    	return result;
    }
    
    private void threeSum(int[] nums, int index, int target, List<List<Integer>> result) {
		for (int j = index + 1; j < nums.length; j++) {
    		if (isDuplicate(j, index + 1, nums)) { // 去重 
    			continue;
    		}
			
			int value = nums[j];
			int left = j + 1; // 左指针
			int right = nums.length - 1; // 右指针
			while (left < right) {
				int sum = value + nums[left] + nums[right];
				if (sum == target) {
					result.add(Arrays.asList(nums[index], nums[j], nums[left], nums[right]));
					// 去重，如 -7 3 4 4 4  ,结果仅为 -7,3,4
					while (left < right && nums[left] == nums[left + 1]) {
						left++;
					}
					while(left < right && nums[right] == nums[right - 1]) {
						right--;
					}
					left++;
					right--;
				} else if (sum > target) {
					right--;
				} else {
					left++;
				}
			}
		}
    }
    
    private boolean isDuplicate(int i, int index, int[] nums) {
    	if (index < 0) {
    		throw new IllegalArgumentException("Index must greater than zero");
    	}
    	if (i > index && nums[i] == nums[i - 1]) {
			return true;
		}
    	return false;
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/4sum/)