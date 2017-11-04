+++
date = "2017-11-02T00:00:00"
draft = false
tags = ["Leetcode", "LinkedList", "Math", "Medium"]
title = "002 - Add Two Numbers"
math = true
summary = """ """

[header]
image = ""
caption = ""

+++

{{% toc %}}

### Problem

You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8

[LINK](https://leetcode.com/problems/add-two-numbers/description/)

### Solution

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def addTwoNumbers(self, l1, l2):
        """
        :type l1: ListNode
        :type l2: ListNode
        :rtype: ListNode
        """
        carry = 0
        dummy_head = pointer = ListNode(0)
        while l1 or l2 or carry:
            x = y = 0
            if l1:
                x = l1.val 
                l1 = l1.next
            if l2:
                y = l2.val
                l2 = l2.next
            pointer.next = ListNode((x + y + carry) % 10)
            carry = (x + y + carry) // 10
            pointer = pointer.next
        return dummy_head.next
```

### Analysis

TC: $O(n)$