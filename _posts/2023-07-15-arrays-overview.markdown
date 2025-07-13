---
title: "Arrays / Lists Overview"
date: 2023-07-15
categories: [data-structures]
tags: [arrays, lists, data-structures, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, time-complexity, big-o, problem-solving]
---

Arrays (or lists in Python) are fundamental data structures that store elements in a contiguous block of memory, allowing fast access by index. This post explains the basics of arrays/lists, their operations, use cases, and provides practical Python examples and problems.

## ✅ What It Is

An **array** (or list in Python) is a collection of elements stored in a contiguous memory block. Each element is accessible by an index.

- Fixed-size in some languages (e.g., arrays in Java, C++)
- Dynamic in others (e.g., lists in Python)

## 🛠️ How to Use (Python)

```python
# Basic array (list) operations in Python
# - Create a list, access elements, add/remove items, insert at a position, remove by value
arr = [1, 2, 3, 4]
print(arr[0])     # Access first element (prints 1)
arr.append(5)     # Add 5 to the end of the list
arr.pop()         # Remove the last element (removes 5)
arr.insert(2, 9)  # Insert 9 at index 2 (arr becomes [1, 2, 9, 3, 4])
arr.remove(2)     # Remove the first occurrence of 2 (arr becomes [1, 9, 3, 4])
# All operations above are O(1) except insert/remove (O(n) in worst case)
```

## 📦 Use Cases

- Leaderboards
- Queues/Stacks (with helper functions)
- Sliding windows
- Indexed lookup
- Dynamic programming tables

## 📘 Sample Problem 1: Two Sum

> Given an array of integers, return indices of two numbers such that they add up to a target.

```python
# This function finds two numbers in the list that add up to the target and returns their indices.
# It uses a dictionary to store previously seen numbers for fast lookup.
def two_sum(nums, target):
    seen = {}  # Store numbers and their indices
    for i, num in enumerate(nums):
        complement = target - num  # The number needed to reach the target
        if complement in seen:
            return [seen[complement], i]  # Found the pair!
        seen[num] = i  # Store the index of the current number
# Time complexity: O(n), Space complexity: O(n)
```

## 📘 Sample Problem 2: Maximum Subarray (Kadane's Algorithm)

> Find the contiguous subarray with the largest sum.

```python
# Kadane's Algorithm: Find the maximum sum of any contiguous subarray.
# It keeps track of the current sum and the maximum found so far.
def max_subarray(nums):
    max_sum = cur_sum = nums[0]  # Start with the first element
    for num in nums[1:]:
        cur_sum = max(num, cur_sum + num)  # Extend or start new subarray
        max_sum = max(max_sum, cur_sum)    # Update max if needed
    return max_sum
# Time complexity: O(n), Space complexity: O(1)
```

## 🔁 Variants

- Rotate array
- Sliding window max/min
- Prefix sum queries
- Rearrangement (Dutch national flag, wiggle sort)

---

