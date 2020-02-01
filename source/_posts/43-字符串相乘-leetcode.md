---
title: 43_字符串相乘_leetcode
date: 2019-12-17 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 43. 字符串相乘

给定两个以字符串形式表示的非负整数 num1 和 num2，返回 num1 和 num2 的乘积，它们的乘积也表示为字符串形式。

>输入: num1 = "123", num2 = "456"
输出: "56088"

**题解：** 用一个数组存储乘积，根据两数相乘特定的位偏移关系求解

```java
class Solution {
    public String multiply(String num1, String num2) {
        if ("0".equals(num1) || "0".equals(num2)) {
            return "0";
        }
        // 99 * 99 值长度小于4位，故设此数组长度
        int[] digits = new int[num1.length() + num2.length()];
        for (int i = num1.length() - 1; i >= 0; i--) {
            for (int j = num2.length() - 1; j >= 0; j--) {
                int digit = (num1.charAt(i) - '0') * (num2.charAt(j) - '0');
                // 如234*567，数组长度为6，设当前i=2,j=2,
                // 则4*7的低位8位于(i+j+1)=5处，高位位于(i+j)处
                digit += digits[i + j + 1]; // 先加低位看是否有新进位
                digits[i + j + 1] = digit % 10;
                digits[i + j] += digit / 10;
            }
        }

        StringBuilder sb = new StringBuilder();
        if (digits[0] != 0) {
            sb.append(digits[0]);
        }
        for (int i = 1; i < digits.length; i++) {
            sb.append(digits[i]);
        }

        return sb.toString();
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/multiply-strings/)