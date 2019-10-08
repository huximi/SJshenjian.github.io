---
title: 22_括号生成_leetcode
date: 2019-10-07 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 22. 括号生成

给出 n 代表生成括号的对数，请你写出一个函数，使其能够生成所有可能的并且有效的括号组合。

>例如，给出 n = 3，生成结果为：
[
  "((()))",
  "(()())",
  "(())()",
  "()(())",
  "()()()"
]

题解： 动态规划： dp(n) = dp(n - 1) + 第n个括号所在的位置
考虑在dp(n)的结果中,最左边一定为左括号,假定该括号对为第n个括号，则以上公式可考虑为
dp(n) = 第n个括号(该左括号位于最左边) + dp(n-1)相对第n个括号的位置(一定在第n个括号的里面或者右边)
公式表示为：dp(n) = "(" + dp(p) + ")" + dp(q) 其中 p + q = n - 1, 0 < p < n
参考： https://leetcode-cn.com/problems/generate-parentheses/solution/zui-jian-dan-yi-dong-de-dong-tai-gui-hua-bu-lun-da/

```java
public class Solution {
	
	public List<String> generateParenthesis(int n) {
		if (n <= 0) {
			return Collections.emptyList();
		}
		if (n == 1) {
			return new LinkedList<>(Arrays.asList("()"));
		}
		
		List<List<String>> dp = new LinkedList<>();
		dp.add(Arrays.asList(""));
		dp.add(new LinkedList<>(Arrays.asList("()")));
	
		for (int i = 2; i <= n; i++) {
			List<String> dpI = new LinkedList<>();
			for (int j = 0; j < i; j++) {
				for (String p : dp.get(j)) {
					for (String q : dp.get(i - 1 - j)) {						
						dpI.add("(" + p + ")" + q);
					}
				}				
			}
			dp.add(dpI);
		}
		return dp.get(n);
	}
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/generate-parentheses/submissions/)