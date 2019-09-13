---
title: 9_回文数_leetcode
date: 2019-09-01 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 9. 回文数

判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

示例 1:
<table><tr><td bgcolor=#f7f9fa>输入: 121
输出: true
</td></tr></table>

示例 2:
<table><tr><td bgcolor=#f7f9fa>输入: -121
输出: false
解释: 从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
</td></tr></table>

示例 3:
<table><tr><td bgcolor=#f7f9fa>输入: 10
输出: false
解释: 从右向左读, 为 01 。因此它不是一个回文数。
</td></tr></table>

```java
class Solution {
    public boolean isPalindrome(int x) {
        // -32等等均不是
        // 120 100等均不是
        if (x < 0 || (x % 10 == 0 && x != 0)) {
            return false;
        }
        // 采用字符串反转方式简单粗暴
        // 进阶采用整数方式解决，回文数只需反转数字长度的1/2即可
        int newX = 0;
        while ( x > newX) {
            newX = newX * 10 + x % 10;
            x /= 10;
        }
        // 返回结果需考虑奇数情况
        return x == newX || newX / 10 == x;
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/palindrome-number/)