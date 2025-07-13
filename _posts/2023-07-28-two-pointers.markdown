---
title: "Two Pointers Technique"
date: 2023-07-28
categories: [algorithms]
tags: [two-pointers, two-pointer, three-pointers, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, array, string, palindrome, pair-sum, in-place, problem-solving, big-o, time-complexity, sliding-window, sorting, merging, deduplication]
---

The Two Pointers technique uses two indices to efficiently solve problems involving pairs, subarrays, or partitions in arrays and strings. This post explains the method, its variations, and provides practical Python examples and problems for mastering two-pointer strategies.

# Two Pointers

## Overview

**Two Pointers** is a technique where two indices move through a list to solve problems involving **pairs, subarrays, or partitioning**.

- Can move in the **same direction** (sliding window-style)
- Or from **both ends toward the center** (converging pointers)
- Useful in sorted arrays and string problems

## 🛠️ How to Use (Python)

```python
# Example: check if array has a pair that sums to target (array must be sorted)
def has_pair_with_sum(arr, target):
    left, right = 0, len(arr) - 1  # Start with two pointers at both ends
    while left < right:
        s = arr[left] + arr[right]  # Sum of the two elements
        if s == target:
            return True  # Found a pair
        elif s < target:
            left += 1    # Move left pointer to increase sum
        else:
            right -= 1   # Move right pointer to decrease sum
    return False  # No pair found
# Time complexity: O(n), Space complexity: O(1)
```

## 📦 Use Cases

- Sorted arrays (two sum, three sum)
- Palindrome checks
- Merging two sorted lists
- Container with most water

## 📘 Sample Problem 1: Move Zeroes

> Move all 0s to the end of the array while maintaining the relative order of non-zero elements.

```python
# This function moves all zeroes to the end of the list in-place.
def move_zeroes(nums):
    left = 0  # Position to place the next non-zero element
    for right in range(len(nums)):
        if nums[right] != 0:
            nums[left], nums[right] = nums[right], nums[left]  # Swap
            left += 1
# Time complexity: O(n), Space complexity: O(1)
```

## 📘 Sample Problem 2: Valid Palindrome

> Return true if the input string is a palindrome (ignore non-alphanumeric).

```python
import re

# This function checks if a string is a palindrome, ignoring non-alphanumeric characters.
def is_palindrome(s):
    s = re.sub('[^a-zA-Z0-9]', '', s).lower()  # Remove non-alphanumeric and lowercase
    left, right = 0, len(s) - 1
    while left < right:
        if s[left] != s[right]:
            return False  # Mismatch found
        left += 1
        right -= 1
    return True  # All characters matched
# Time complexity: O(n), Space complexity: O(1)
```

## 🔁 Variants

- Three pointers (for color sort or deduplication)
- Bidirectional scanning
- Merging two-pointer + binary search

---

