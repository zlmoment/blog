+++
date = "2017-10-29T10:00:00"
draft = false
tags = ["Leetcode", "Array", "HashTable", "Easy"]
title = "001 - Two Sum"
math = true
summary = """ """

[header]
image = ""
caption = ""

+++

{{% toc %}}

### Problem

Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

Example:
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].

[LINK](https://leetcode.com/problems/two-sum/description/)

### Solution

```python
class Solution:
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        """
        1775 ms
        for i in range(len(nums)):
            a = nums[i]
            b = target - a
            if b in nums and nums.index(b) != i:
                return i, nums.index(b)
        """
        """
        62 ms with two passes dict solution
        nums_dict = {}
        for i, v in enumerate(nums):
            nums_dict[v] = i
        for i, v in enumerate(nums):
            if target - v in nums_dict and nums_dict[target - v] != i:
                return i, nums_dict[target - v]
        """
        """
        65 ms with one pass dict solution
        """
        nums_dict = {}
        for i, v in enumerate(nums):
            if target - v in nums_dict:
                return nums_dict[target - v], i
            nums_dict[v] = i
```

### Analysis

TC: $O(n)$