---
title: 2_两数之加_leetcode
date: 2019-08-13 22:19:17
category: 算法与数据结构
tag: [算法与数据结构, Leetcode]
---

## 2. 两数之加

给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        int carry = 0; // 存储进位
        ListNode resultNode = new ListNode(0);
        ListNode firstNode = l1;
        ListNode secondNode = l2;
        ListNode currentNode = resultNode;

        while (firstNode != null || secondNode != null) {
            int firstNodeValue = firstNode != null ? firstNode.val : 0;
            int secondNodeValue = secondNode != null ? secondNode.val : 0;
            int sum = carry + firstNodeValue + secondNodeValue;

            currentNode.next = new ListNode(sum % 10);
            carry = sum / 10;

            currentNode = currentNode.next;
            if (firstNode != null) {
                firstNode = firstNode.next;
            }
            if (secondNode != null) {
                secondNode = secondNode.next;
            }
        }
        if (carry > 0) { // 最后一位也可能进位
            currentNode.next = new ListNode(carry);
        }
        return resultNode.next;
    }
}
class ListNode {
      int val;
      ListNode next;
      ListNode(int x) { val = x; }
}
```

AC传送门[leetcode](https://leetcode-cn.com/problems/add-two-numbers/)