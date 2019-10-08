---
title: 17_电话号码的字母组合_leetcode
date: 2019-09-18 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 17. 电话号码的字母组合

给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

![电话号码的字母组合](17-电话号码的字母组合-leetcode/17_telephone_keypad.png)

>输入："23"
输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].

题解： 利用队列先进先出特性求解，类似二叉树的层次遍历求解方式,建议断点调试理解求解过程

```java
class Solution {
	
	public List<String> letterCombinations(String digits) {
		LinkedList<String> result = new LinkedList<>();
		if (digits == null || digits.isEmpty()) {
			return result;
		}
		// 数字为下标,值为数字可对应的字母
		String[] mapping = {"0", "1", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
		
		result.add("");
		for (int i = 0; i < digits.length(); i++) {
			int digit = Character.getNumericValue(digits.charAt(i));
			// 控制出入队列内容, 长度为i+1的数字组合依赖于长度为i的组合 + 该数字可对应的字母
			while (result.peek().length() == i) {
				String prevValue = result.remove(); // 长度为i的组合出队列
				for (char c : mapping[digit].toCharArray()) { // 该数字可对应的字母
					result.add(prevValue + c); // 长度为i+1的数字组合入队列
				}
			}
		}
		
		return result;
	}
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)