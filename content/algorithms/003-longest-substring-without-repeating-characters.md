+++
date = "2017-11-08T00:00:00"
draft = false
tags = ["Leetcode", "HashTable", "TwoPointers", "String", "Medium"]
title = "003 - Longest Substring Without Repeating Characters"
math = true
summary = """ """

[header]
image = ""
caption = ""

+++

{{% toc %}}

### Problem

Given a string, find the length of the longest substring without repeating characters.

Examples:

Given `"abcabcbb"`, the answer is "abc", which the length is 3.

Given `"bbbbb"`, the answer is "b", with the length of 1.

Given `"pwwkew"`, the answer is "wke", with the length of 3. Note that the answer must be a substring, "pwke" is a subsequence and not a substring.


[LINK](https://leetcode.com/problems/longest-substring-without-repeating-characters/description/)

### Solution

My solution is using a list `temp` to store the string and for each char in the `s`, I check if this char has already been in `temp`. If yes, then delete the substring before and including the char. 

```python
class Solution:
    def lengthOfLongestSubstring(self, s):
        """
        :type s: str
        :rtype: int
        """
        length = 0
        max_length = 0
        temp = []
        for l in s:
            if l in temp:
                if len(temp) > max_length:
                    max_length = len(temp)
                index = temp.index(l)
                temp = temp[index + 1:]
                length -= index + 1
            temp.append(l)
            length += 1
        return max(length, max_length)
        
```

The following code is from the submission using the smallest running time:

```python
class Solution:
    def lengthOfLongestSubstring(self, s):
        """
        :type s: str
        :rtype: int
        """
        long=""
        i=0
        for x in s:
            if x not in long:
                long=long+x
            else:
                long=long.partition(x)[2]+x
            if i<len(long):
                i=len(long)
        return i
```

This solution is better than mine in the following points:

1. Use string `long` instead of a list `temp`.
2. Return a single value instead of a max function.
3. Use `partition` built-in function instead of list slice.

**str.partition(sep)**
Split the string at the first occurrence of sep, and return a 3-tuple containing the part before the separator, the separator itself, and the part after the separator. If the separator is not found, return a 3-tuple containing the string itself, followed by two empty strings.

