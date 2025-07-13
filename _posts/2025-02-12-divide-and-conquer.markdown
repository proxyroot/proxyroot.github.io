---
title: "Divide & Conquer"
date: 2025-02-12
categories: [algorithms]
tags: [divide-and-conquer, recursion, python]
---

Divide & Conquer is a recursive algorithm design paradigm that breaks problems into smaller subproblems, solves them independently, and combines their solutions. This post explains the divide and conquer approach, its use cases, and provides practical Python examples and problems for mastering this technique.

## Overview

**Divide and Conquer** is a recursive algorithm design paradigm that breaks a problem into smaller subproblems, solves them recursively, and combines their solutions.

- Divide ➝ split the input
- Conquer ➝ recursively solve subproblems
- Combine ➝ merge or process the results

## 🛠️ Common Steps

1. Identify base case
2. Divide input into subparts
3. Solve subproblems recursively
4. Merge results

## 📦 Use Cases

- Sorting algorithms (Merge Sort, Quick Sort)
- Binary Search
- Closest pair of points
- Matrix multiplication (Strassen)
- Count inversions

## 📘 Sample Problem 1: Merge Sort

```python
# This function sorts an array using the merge sort algorithm (divide and conquer).
def merge_sort(arr):
    if len(arr) <= 1:
        return arr  # Base case: already sorted
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])    # Sort left half
    right = merge_sort(arr[mid:])   # Sort right half
    return merge(left, right)       # Merge sorted halves

# Helper function to merge two sorted arrays
def merge(left, right):
    result = []
    i = j = 0
    while i < len(left) and j < len(right):
        if left[i] < right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    result.extend(left[i:])  # Add remaining elements
    result.extend(right[j:])
    return result
# Time complexity: O(n log n), Space: O(n)
```

## 📘 Sample Problem 2: Count Inversions

> Count the number of pairs (i, j) such that i < j and arr[i] > arr[j].

```python
# This function counts the number of inversions in an array using merge sort.
def count_inversions(arr):
    def merge_sort(nums):
        if len(nums) <= 1:
            return nums, 0  # Base case: no inversions
        mid = len(nums) // 2
        left, inv_left = merge_sort(nums[:mid])
        right, inv_right = merge_sort(nums[mid:])
        merged, inv_merge = merge_count(left, right)
        return merged, inv_left + inv_right + inv_merge

    def merge_count(left, right):
        merged = []
        i = j = inv_count = 0
        while i < len(left) and j < len(right):
            if left[i] <= right[j]:
                merged.append(left[i])
                i += 1
            else:
                merged.append(right[j])
                inv_count += len(left) - i  # Count inversions
                j += 1
        merged += left[i:]
        merged += right[j:]
        return merged, inv_count

    _, total_inv = merge_sort(arr)
    return total_inv
# Time complexity: O(n log n), Space: O(n)
```

## 🔁 Variants

- Fast Fourier Transform (FFT)
- Quickselect (Kth element)
- Karatsuba multiplication
- Binary search problems

---

