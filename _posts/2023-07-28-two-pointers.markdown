---
title: "Two Pointers Technique"
date: 2023-07-28
categories: [algorithms]
tags: [two-pointers, python]
---

# Two Pointers

## ✅ What It Is

**Two Pointers** is a technique where two indices move through a list to solve problems involving **pairs, subarrays, or partitioning**.

- Can move in the **same direction** (sliding window-style)
- Or from **both ends toward the center** (converging pointers)
- Useful in sorted arrays and string problems

## 🛠️ How to Use (Python)

```python
# Example: check if array has a pair that sums to target (array must be sorted)

def has_pair_with_sum(arr, target):
    left, right = 0, len(arr) - 1
    while left < right:
        s = arr[left] + arr[right]
        if s == target:
            return True
        elif s < target:
            left += 1
        else:
            right -= 1
    return False
```

## 📦 Use Cases

- Sorted arrays (two sum, three sum)
- Palindrome checks
- Merging two sorted lists
- Container with most water

## 📘 Sample Problem 1: Move Zeroes

> Move all 0s to the end of the array while maintaining the relative order of non-zero elements.

```python
def move_zeroes(nums):
    left = 0
    for right in range(len(nums)):
        if nums[right] != 0:
            nums[left], nums[right] = nums[right], nums[left]
            left += 1
```

## 📘 Sample Problem 2: Valid Palindrome

> Return true if the input string is a palindrome (ignore non-alphanumeric).

```python
import re

def is_palindrome(s):
    s = re.sub('[^a-zA-Z0-9]', '', s).lower()
    left, right = 0, len(s) - 1
    while left < right:
        if s[left] != s[right]:
            return False
        left += 1
        right -= 1
    return True
```

## 🔁 Variants

- Three pointers (for color sort or deduplication)
- Bidirectional scanning
- Merging two-pointer + binary search

---

