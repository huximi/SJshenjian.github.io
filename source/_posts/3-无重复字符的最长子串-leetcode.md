---
title: 3_无重复字符的最长子串_leetcode
date: 2019-08-18 10:26:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 3. 无重复字符的最长子串

给定一个字符串，请你找出其中不含有重复字符的**最长子串**的长度。

示例 1:

输入: "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。

输入: "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。


```java
/**
 * 思路：end遍历字符串,更新start[更新采用的方式决定了算法的效率],更新longestLength
 */
public class Solution {

    public int lengthOfLongestSubstring(String s) {
        if (s == null) {
            return 0;
        }

        int start = 0; // 设定不重复元素左边界
        int end = 0; // 设定不重复元素右边界
        int longestLength = 0;
        // key为当前遍历的元素,value为当前
        // end为不重复右边界，end+1在遍历时可能重复，可能不重复
        // 若下一次遍历时，重复，则更新start
        Map<Character, Integer> map = new HashMap<>();

        for (end = 0; end < s.length(); end++) {
            char c = s.charAt(end);
            if (map.containsKey(c)) {
                // 需要仔细理解此处，由于之前的设定，[start, end]间为最长结果值，即end-start+1,
                // 由于map中包含c值，故在[start,end]间必定存在元素与c值相同，
                // 最好情况为start与c值相同，即最大长度为end-start+1，换言之，在(start,end]间，最大长度一定小于end-start+1
                // 故start为以下取值方式
                start = Math.max(map.get(c) + 1, start);
            }
            longestLength = Math.max(end - start + 1, longestLength);
            map.put(c, end); // 存储当前的不重复最大end边界
        }
        return longestLength;
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)