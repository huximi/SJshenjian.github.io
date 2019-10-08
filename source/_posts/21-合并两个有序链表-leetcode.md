---
title: 21_合并两个有序链表_leetcode
date: 2019-09-25 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 21. 合并两个有序链表

将两个有序链表合并为一个新的有序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

>示例：
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4

题解： 送分题奥，请看代码吧

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) { 
    	if (l1 == null) return l2;
    	if (l2 == null) return l1;
    	
    	ListNode pre = new ListNode(0);
    	ListNode temp = pre;
    	while (l1 != null && l2 != null) {
    		if (l1.val < l2.val) {
    			temp.next = l1;
    			l1 = l1.next;
    		} else {
    			temp.next = l2;
    			l2 = l2.next;
    		}
    		temp = temp.next;
    	}
    	
    	temp.next = l1 != null ? l1 : l2;    	
    	return pre.next;
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/merge-two-sorted-lists)