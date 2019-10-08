---
title: 19_删除链表的倒数第N个节点_leetcode
date: 2019-09-23 22:16:28
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 19. 删除链表的倒数第N个节点

给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。

>示例：
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
说明：
给定的 n 保证是有效的。

题解： 双指针节点一次遍历法
第1个指针向前走n步，然后1与2一起向前走，直到1到达尾节点
这保证了1与2指针间据为n,此时指针2的下一个节点为待删除节点，删除即可

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
      ListNode pre = new ListNode(0);
       pre.next = head;
       ListNode first = pre;
       ListNode second = pre;
       
       while (n > 0) { // 第1个指针向前走n步
    	   first = first.next;
    	   n--;
       }
       
       while (first.next != null) { // 1与2一起向前走，直到1到达尾节点
    	   first = first.next;
    	   second = second.next;
       }
       second.next = second.next.next; // 删除节点
       return pre.next;
    }
}
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
```

AC传送门[leetcode](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list)