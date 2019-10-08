---
title: 14_最长公共前缀_leetcode
date: 2019-09-08 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 14. 最长公共前缀

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 ""。

>输入: ["flower","flow","flight"]
输出: "fl"

>输入: ["dog","racecar","car"]
输出: ""
解释: 输入不存在公共前缀。
说明:所有输入只包含小写字母 a-z 。

题解： 二分查找求解最长公共前缀

```java
class Solution {
	public String longestCommonPrefix(String[] strs) {
		if (strs == null || strs.length == 0) {
			return "";
		}
		int len = Integer.MAX_VALUE;
		for (String str : strs) {
			len = Math.min(len, str.length());
		}
		
		int low = 0;
		int high = len;
		while (low <= high) {
			int middle  = (low + high) / 2;
			if (isCommonPrefix(strs, middle)) {
				low = middle + 1;
			} else {
				high = middle - 1;
			}
		}
		
		return strs[0].substring(0, (low + high) / 2);
	}
	
	private boolean isCommonPrefix(String[] strs, int len) {
		String str = strs[0].substring(0, len);
		for (int i = 1; i < strs.length; i++) {
			if (!strs[i].startsWith(str)) {
				return false;
			}
		}
		return true;
	}
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/longest-common-prefix/submissions/)