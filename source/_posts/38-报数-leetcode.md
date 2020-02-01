---
title: 38_报数_leetcode
date: 2019-11-30 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 38. 报数

报数序列是一个整数序列，按照其中的整数的顺序进行报数，得到下一个数。其前五项如下：

>1.     1
2.     11
3.     21
4.     1211
5.     111221
1 被读作  "one 1"  ("一个一") , 即 11。
11 被读作 "two 1" ("两个一"）, 即 21。
21 被读作 "one 2",  "one 1" （"一个二" ,  "一个一") , 即 1211。

给定一个正整数 n（1 ≤ n ≤ 30），输出报数序列的第 n 项。
注意：整数顺序将表示为一个字符串。


**题解：** 简单，关键在于理解题意

```java
class Solution {
    public String countAndSay(int n) {
        String pre = "1";
        StringBuilder sb = new StringBuilder();
        for (int i = 1; i < n; i++) {
            char ch = pre.charAt(0);
            int count = 0;
            sb.delete(0, sb.length());
            for (char curr : pre.toCharArray()) {
                if (curr == ch) {
                    count++;
                } else {
                    sb.append(count).append(ch);
                    // 重新计数
                    ch = curr;
                    count = 1;
                }
            }
            sb.append(count).append(ch);
            pre = sb.toString();
        }
        return pre;
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/count-and-say/)