---
title: 24_两两交换链表中的节点_leetcode
date: 2019-10-08 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 24. 两两交换链表中的节点

给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

>示例:
给定 1->2->3->4, 你应该返回 2->1->4->3.

题解： 三个主要节点： temp用于遍历，start end用于交换

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
    public ListNode swapPairs(ListNode head) {
    	ListNode prev = new ListNode(0);
    	prev.next = head;
    	ListNode temp = prev;
    	
    	while (temp.next != null && temp.next.next != null) {
    		ListNode start = temp.next;
    		ListNode end = temp.next.next;
    		temp.next = end;
    		start.next = end.next;
    		end.next = start;
    		temp = start;
    	}
    	return prev.next;
    }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)