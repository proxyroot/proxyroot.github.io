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

---

## 🧩 Visualizing Divide & Conquer

### General Structure

```
Problem: [8, 3, 5, 4, 7, 6, 1, 2]

Divide:     [8, 3, 5, 4]    [7, 6, 1, 2]
Divide:   [8, 3] [5, 4]   [7, 6] [1, 2]
Divide:  [8] [3] [5] [4] [7] [6] [1] [2]

Conquer:   [3, 8] [4, 5]   [6, 7] [1, 2]
Conquer:     [3, 4, 5, 8]    [1, 2, 6, 7]

Combine:        [1, 2, 3, 4, 5, 6, 7, 8]
```

### Merge Sort Example

```
Input: [8, 3, 5, 4, 7, 6, 1, 2]

Level 1: [8, 3, 5, 4] [7, 6, 1, 2]
Level 2: [8, 3] [5, 4] [7, 6] [1, 2]
Level 3: [8] [3] [5] [4] [7] [6] [1] [2]

Merge: [3, 8] [4, 5] [6, 7] [1, 2]
Merge: [3, 4, 5, 8] [1, 2, 6, 7]
Merge: [1, 2, 3, 4, 5, 6, 7, 8]
```

---

## 🛠️ Common Steps

1. Identify base case
2. Divide input into subparts
3. Solve subproblems recursively
4. Merge results

```python
def divide_and_conquer(problem):
    # Base case
    if problem is small enough:
        return solve_directly(problem)
    
    # Divide
    subproblems = divide(problem)
    
    # Conquer
    solutions = [divide_and_conquer(sub) for sub in subproblems]
    
    # Combine
    return combine(solutions)
```

---

## 🧩 Merge Sort Step-by-Step

Suppose arr = [8, 3, 5, 4]

| Level | arr | mid | left | right | action |
|-------|-----|-----|------|-------|--------|
| 1     | [8,3,5,4] | 2 | [8,3] | [5,4] | Divide |
| 2     | [8,3] | 1 | [8] | [3] | Divide |
| 2     | [5,4] | 1 | [5] | [4] | Divide |
| 3     | [8] | - | - | - | Base case |
| 3     | [3] | - | - | - | Base case |
| 3     | [5] | - | - | - | Base case |
| 3     | [4] | - | - | - | Base case |
| 2     | [3,8] | - | - | - | Merge [3] + [8] |
| 2     | [4,5] | - | - | - | Merge [4] + [5] |
| 1     | [3,4,5,8] | - | - | - | Merge [3,8] + [4,5] |

- Final result: [3, 4, 5, 8]

---

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

# Example:
arr = [8, 3, 5, 4, 7, 6, 1, 2]
sorted_arr = merge_sort(arr)
print(sorted_arr)  # Output: [1, 2, 3, 4, 5, 6, 7, 8]
```

---

## 🧩 Count Inversions Flow

Suppose arr = [8, 3, 5, 4]

| Level | arr | left | right | inv_left | inv_right | inv_merge | total_inv |
|-------|-----|------|-------|----------|-----------|-----------|-----------|
| 1     | [8,3,5,4] | [8,3] | [5,4] | 1 | 1 | 2 | 4 |
| 2     | [8,3] | [8] | [3] | 0 | 0 | 1 | 1 |
| 2     | [5,4] | [5] | [4] | 0 | 0 | 1 | 1 |
| 3     | [8] | - | - | 0 | 0 | 0 | 0 |
| 3     | [3] | - | - | 0 | 0 | 0 | 0 |
| 3     | [5] | - | - | 0 | 0 | 0 | 0 |
| 3     | [4] | - | - | 0 | 0 | 0 | 0 |

- Inversions: (8,3), (8,5), (8,4), (5,4) = 4 total inversions.

---

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

# Example:
arr = [8, 3, 5, 4]
inversions = count_inversions(arr)
print(inversions)  # Output: 4
```

---

## 🔁 Variants

- Fast Fourier Transform (FFT)
- Quickselect (Kth element)
- Karatsuba multiplication
- Binary search problems

---

