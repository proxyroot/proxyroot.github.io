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

---

## 🧩 Visualizing Two Pointers

### Converging Pointers (Two Sum in Sorted Array)

Suppose arr = [1, 3, 5, 7, 9], target = 8

```
Initial:  [1, 3, 5, 7, 9]
          ↑           ↑
         left       right

Step 1:   [1, 3, 5, 7, 9]  (1+9=10 > 8, move right)
          ↑         ↑
         left     right

Step 2:   [1, 3, 5, 7, 9]  (1+7=8, found!)
          ↑       ↑
         left   right
```

---

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

# Example:
arr = [1, 3, 5, 7, 9]
target = 8
print(has_pair_with_sum(arr, target))  # Output: True (1+7=8)
```

---

## 📦 Use Cases

- Sorted arrays (two sum, three sum)
- Palindrome checks
- Merging two sorted lists
- Container with most water

---

## 🧩 Move Zeroes Step-by-Step

Suppose nums = [0, 1, 0, 3, 12]

| Step | left | right | nums[right] | nums[left] | Action           | Array State        |
|------|------|-------|-------------|------------|------------------|-------------------|
| 1    | 0    | 0     | 0           | 0          | Skip (zero)      | [0, 1, 0, 3, 12]  |
| 2    | 0    | 1     | 1           | 0          | Swap             | [1, 0, 0, 3, 12]  |
| 3    | 1    | 2     | 0           | 0          | Skip (zero)      | [1, 0, 0, 3, 12]  |
| 4    | 1    | 3     | 3           | 0          | Swap             | [1, 3, 0, 0, 12]  |
| 5    | 2    | 4     | 12          | 0          | Swap             | [1, 3, 12, 0, 0]  |

---

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

# Example:
nums = [0, 1, 0, 3, 12]
move_zeroes(nums)
print(nums)  # Output: [1, 3, 12, 0, 0]
```

---

## 🧩 Palindrome Check Flow

Suppose s = "A man, a plan, a canal: Panama"

1. Clean string: "amanaplanacanalpanama"
2. Two pointers converge from both ends:

```
Step 1: "amanaplanacanalpanama"
         ↑                   ↑
        left              right

Step 2: "amanaplanacanalpanama"
          ↑                 ↑
         left             right

Step 3: "amanaplanacanalpanama"
           ↑               ↑
          left           right

... (continues until pointers meet)
```

---

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

# Example:
s = "A man, a plan, a canal: Panama"
print(is_palindrome(s))  # Output: True
```

---

## 🔁 Variants

- Three pointers (for color sort or deduplication)
- Bidirectional scanning
- Merging two-pointer + binary search

---

