---
title: 20_有效的括号_leetcode
date: 2019-09-24 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 20. 有效的括号

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。

有效字符串需满足：
左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。
注意空字符串可被认为是有效字符串。

>输入: "()[]{}"
输出: true
输入: "(]"
输出: false
输入: "([)]"
输出: false
输入: "{[]}"
输出: true

题解： 利用栈的先入后出特性,遍历至左括号则入栈，右括号则取栈顶元素(此时栈为空则直接false)看是否为对应左括号,
若为是，则继续遍历，若为否，则false

```java
class Solution {
    public boolean isValid(String s) {
        if (s == null || s.length() <= 0) {
        	return true;
        }
        Stack<Character> stack = new Stack<>();
        
        for (char c : s.toCharArray()) {
        	if (c == '(' || c == '[' || c== '{') {
        		stack.push(c);
        	} else if (c == ')' || c == ']' || c== '}') {
        		if (stack.isEmpty()) {     		
        			return false;
        		}
        		if (c == ')' && stack.peek() != '(') {
					return false;
				}	
        		if (c == ']' && stack.peek() != '[') {
					return false;
				}	
        		if (c == '}' && stack.peek() != '{') {
					return false;
				}
        		stack.pop();
        	}
        }
        
        return stack.isEmpty();
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/valid-parentheses/)