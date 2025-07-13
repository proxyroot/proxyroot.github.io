---
title: "Divide & Conquer"
date: 2025-02-12
categories: [algorithms]
tags: [divide-and-conquer, recursion, python]
---

# Divide & Conquer

## ✅ What It Is

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
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    return merge(left, right)

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
    result.extend(left[i:])
    result.extend(right[j:])
    return result
```

## 📘 Sample Problem 2: Count Inversions

> Count the number of pairs (i, j) such that i < j and arr[i] > arr[j].

```python
def count_inversions(arr):
    def merge_sort(nums):
        if len(nums) <= 1:
            return nums, 0
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
                inv_count += len(left) - i
                j += 1
        merged += left[i:]
        merged += right[j:]
        return merged, inv_count

    _, total_inv = merge_sort(arr)
    return total_inv
```

## 🔁 Variants

- Fast Fourier Transform (FFT)
- Quickselect (Kth element)
- Karatsuba multiplication
- Binary search problems

---

