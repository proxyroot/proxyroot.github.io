---
title: "Arrays / Lists Overview"
date: 2023-07-15
categories: [data-structures]
tags: [arrays, lists, python]
---

Arrays (or lists in Python) are fundamental data structures that store elements in a contiguous block of memory, allowing fast access by index. This post explains the basics of arrays/lists, their operations, use cases, and provides practical Python examples and problems.

## ✅ What It Is

An **array** (or list in Python) is a collection of elements stored in a contiguous memory block. Each element is accessible by an index.

- Fixed-size in some languages (e.g., arrays in Java, C++)
- Dynamic in others (e.g., lists in Python)

## 🛠️ How to Use (Python)

```python
arr = [1, 2, 3, 4]
print(arr[0])     # Access first element
arr.append(5)     # Add to end
arr.pop()         # Remove from end
arr.insert(2, 9)  # Insert 9 at index 2
arr.remove(2)     # Remove first occurrence of 2
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
def two_sum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
```

## 📘 Sample Problem 2: Maximum Subarray (Kadane's Algorithm)

> Find the contiguous subarray with the largest sum.

```python
def max_subarray(nums):
    max_sum = cur_sum = nums[0]
    for num in nums[1:]:
        cur_sum = max(num, cur_sum + num)
        max_sum = max(max_sum, cur_sum)
    return max_sum
```

## 🔁 Variants

- Rotate array
- Sliding window max/min
- Prefix sum queries
- Rearrangement (Dutch national flag, wiggle sort)

---

