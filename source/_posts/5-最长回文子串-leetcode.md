---
title: 5_最长回文子串_leetcode
date: 2019-08-26 23:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 5. 最长回文子串

给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。

示例 1：

输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
示例 2：

输入: "cbbd"
输出: "bb"


```java
/**
 *  思路：中心化扩展算法
 *  回文中心两侧互为镜像，以当前位置为中心，向外扩展查找
 *  设start为当前位置回文串最左侧, end为当前位置回文串最右侧
 *  时间复杂度 O(n^2) 空间复杂度 O(1)
 *  感兴趣读者可尝试采用Manacher算法，时间复杂度 O(n)
 */
public class Solution {

    public String longestPalindrome(String s) {
        if (s == null || s.length() < 1) {
            return "";
        }
        int start = 0;
        int end = 0;

        for (int i = 0; i < s.length(); i++) {
            // 回文串中心与两侧数值不相等 即aba形式
            // 回文串中心与右侧数值相等 即abba形式
            // 由于i从0开始遍历，故回文串中心与左侧数值相等无需考虑
            int lengthOne = expandAroundCenter(s, i, i); // aba
            int lengthTwo = expandAroundCenter(s, i, i + 1); // abba

            int len = Math.max(lengthOne, lengthTwo);
            if (len > end - start) {
                start = i - (len - 1) / 2; // 更新回文串最左侧
                end = i + len / 2; // 更新回文串最右侧
            }
        }

        return s.substring(start, end + 1);
    }

    private int expandAroundCenter(String s, int left, int right) {
        int L = left, R = right;
        while (L >= 0 && R < s.length() && s.charAt(L) == s.charAt(R)) {
            // 向两边扩展
            L--;
            R++;
        }
        return R - L - 1; // 返回当前最大长度
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/longest-palindromic-substring/)