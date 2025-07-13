---
title: "Kadane’s Algorithm Overview"
date: 2023-03-19
categories: [algorithms]
tags: [array, dp, kadane, subarray, optimization]
---

Kadane’s Algorithm is a classic dynamic programming technique for finding the maximum sum of a contiguous subarray in linear time. It is elegant, efficient, and a staple in coding interviews. This post explains the intuition, implementation, and variants of Kadane’s Algorithm, with annotated code for beginners.

---

## What is Kadane’s Algorithm?

Kadane’s algorithm finds the **maximum sum of a contiguous subarray** in a 1D array.

- Time complexity: **O(n)**
- Space complexity: **O(1)**

---

## Example Problem

> Given an array of integers, find the contiguous subarray with the largest sum.

```python
# Kadane's Algorithm: Find the maximum sum of a contiguous subarray.
# Time Complexity: O(n), Space Complexity: O(1)
def max_subarray_sum(nums):
    # Initialize both max_sum and curr_sum to the first element
    max_sum = curr_sum = nums[0]

    for num in nums[1:]:
        # At each step, either extend the current subarray or start a new one
        curr_sum = max(num, curr_sum + num)
        # Update the global maximum if needed
        max_sum = max(max_sum, curr_sum)

    return max_sum

# Example usage:
# nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
# print(max_subarray_sum(nums))  # Output: 6 ([4, -1, 2, 1])
```
# Approach:
# - Maintain a running sum (curr_sum).
# - At each step, decide whether to extend the current subarray or start fresh.
# - Keep track of the maximum sum seen so far (max_sum).
# - If the running sum becomes less than the current number, start a new subarray.
# - Negative sums are discarded.

---

## Use Cases

- Stock profit (single transaction)
- Maximum rainfall in consecutive days
- Temperature swings
- Subarrays with score-based evaluation

---

## Variants

- Return the actual subarray (store start and end indices)
- 2D Kadane’s (maximum sum submatrix)
- Minimum subarray sum (flip sign logic)
- Max product subarray, circular subarray

---

Kadane's is fast, intuitive, and one of the most effective algorithms in your coding toolbox. 