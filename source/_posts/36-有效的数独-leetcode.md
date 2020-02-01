---
title: 36_有效的数独_leetcode
date: 2019-11-29 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 36. 有效的数独

判断一个 9x9 的数独是否有效。只需要根据以下规则，验证已经填入的数字是否有效即可。

数字 1-9 在每一行只能出现一次。
数字 1-9 在每一列只能出现一次。
数字 1-9 在每一个以粗实线分隔的 3x3 宫内只能出现一次。

**题解：** 根据题意，需满足以上三个条件，故采用三数组记录数字是否满足条件即可，关键在于3x3宫的巧妙设计，详见代码

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
       boolean[][] row = new boolean[9][9]; // 记录行中是否存在重复数字
       boolean[][] column = new boolean[9][9]; // 记录列中是否存在重复数字
       boolean[][] block = new boolean[9][9]; // 记录3*3宫中是否存在重复数组

        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                if ( board[i][j] != '.') {
                    // i是行标，j是列标。行标决定一组block的起始位置
                    // 因为block为3行，所以除3取整得到组号，又因为每组block为3个，所以需要乘3
                    // 列标再细分出是哪个block（因为block是3列，所以除3取整）
                    int num =  board[i][j] - '1';
                    int blockIndex = i / 3 * 3 + j / 3;
                    if (row[i][num] || column[j][num] || block[blockIndex][num]) {
                        return false;
                    }
                    row[i][num] = true;
                    column[j][num] = true;
                    block[blockIndex][num] = true;
                }
            }
        }
        return true;
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/valid-sudoku)