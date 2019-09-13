---
title: 7_整数反转_leetcode
date: 2019-08-29 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 7. 整数反转

给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

示例 1:

输入: 123
输出: 321
 示例 2:

输入: -123
输出: -321
示例 3:

输入: 120
输出: 21
注意:

假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 [−231,  231 − 1]。请根据这个假设，如果反转后整数溢出那么就返回 0。

```java
class Solution {
    public int reverse(int x) {
      int result = 0;
		while (x != 0) {
			int theUnit = x % 10; // x的个位
			// [-2147483648,2147483647]
			if (result > Integer.MAX_VALUE / 10 || (result == Integer.MAX_VALUE / 10 && theUnit > 7)) {
				return 0;
			}
			if (result < Integer.MIN_VALUE / 10 || (result == Integer.MIN_VALUE / 10 && theUnit < -8) ) {
				return 0;
			}
			
			result = result * 10 + theUnit;
			x /= 10; // 剔除x最后一位
		}
		return result;
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/reverse-integer/)