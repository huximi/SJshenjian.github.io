---
title: 49_字母异位词分组_leetcode
date: 2010-01-05 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。
说明：所有输入均为小写字母。不考虑答案输出的顺序。

>输入: ["eat", "tea", "tan", "ate", "nat", "bat"],
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]

**题解：**  字典表 + 哈希表

```java
/**
 * 字符共26个,故可设置字典表数组大小为26，将串映射到字典表中，
 * 然后将字典表中value拼接成key,存储到哈希表判断即可
 */
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<>();
        for (int i = 0; i < strs.length; i++) {
            int[] nums = new int[26];
            for (int j = 0; j < strs[i].length(); j++) {
                nums[strs[i].charAt(j) - 'a']++;
            }
            StringBuilder sb = new StringBuilder();
            for (int j = 0; j < nums.length; j++) {
                sb.append(nums[j]);
            }
            String key = sb.toString();
            if (map.get(key) != null) {
                map.get(key).add(strs[i]);
            } else {
                List<String> value = new ArrayList<>();
                value.add(strs[i]);
                map.put(key, value);
            }
        }

        return new ArrayList<>(map.values());
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/group-anagrams/)