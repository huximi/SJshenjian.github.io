---
title: 28_实现strStr_leetcode
date: 2019-10-13 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 28. 实现strStr()

实现 strStr() 函数。
给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回  -1。

>输入: haystack = "hello", needle = "ll"
输出: 2
输入: haystack = "aaaaa", needle = "bba"
输出: -1


题解： **Sunday算法** 
 * 设originIndex为待查找串index,aimIndex为目标串index.
 * 从左向右依次匹配
 * 若匹配则index均加1继续匹配，直到目标串全部匹配完，则找到
 * 若不匹配则查看(originIndex - aimIndex + 目标串长度 )的字符是否在目标串中存在
 * 特别注意[originIndex - aimIndex 的意义为当前匹配中第一次开始匹配的index, 理解这一点非常重要 !!!!]
 *  若不存在，则originIndex设置为距离当前查看字符的下一次字符，aimIndex设置为0，继续循环匹配
 *  若存在，则在当前查看字符处按其在aim中最后一次出现的位置处对其，aimIndex设置为0，继续循环匹配

```java
class Solution {
    public int strStr(String origin, String aim) {
        if (origin == null || aim == null) {
            return 0;
        }
        if (origin.length() < aim.length()) {
            return -1;
        }
        
        int originIndex = 0;
        int aimIndex = 0;
        // 成功匹配完终止条件：所有aim均成功匹配
        while (aimIndex < aim.length()) {
            // 针对origin匹配完，但aim未匹配完情况处理 如 mississippi sippia    
            if (originIndex > origin.length() - 1) {
                return -1;
            }
            if (origin.charAt(originIndex) == aim.charAt(aimIndex)) {
                // 匹配则index均加1
                originIndex++;
                aimIndex++;
            } else {
                // 获取当前不匹配的下一个距离源index即(originIndex - aimIndex)长度为aim.length()的字符
                int nextCharIndex = originIndex - aimIndex + aim.length();
                if (nextCharIndex < origin.length()) {
                    // 判断是否该字符在aim中存在，若存在，返回最后一个匹配的index
                    int loc = aim.lastIndexOf(origin.charAt(nextCharIndex));  
                    if (loc == -1) {
                        // 该字符不存在于aim中则设置originIndex为该字符的下一个字符
                        originIndex = nextCharIndex + 1;
                    } else {
                        // 该字符存在于aim中则设置originIndex使其在aim中该字符处对齐 
                        originIndex = nextCharIndex - loc;
                    }               
                    aimIndex = 0;
                } else {
                    return -1;
                }
            }
        }
        return originIndex - aimIndex;
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/implement-strstr/)