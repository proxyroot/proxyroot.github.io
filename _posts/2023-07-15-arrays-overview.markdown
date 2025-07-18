---
title: "Arrays / Lists Overview"
date: 2023-07-15
categories: [data-structures]
tags: [arrays, lists, data-structures, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, time-complexity, big-o, problem-solving]
---

Arrays (or lists in Python) are fundamental data structures that store elements in a contiguous block of memory, allowing fast access by index. This post explains the basics of arrays/lists, their operations, use cases, and provides practical Python examples and problems.

## Overview

An **array** (or list in Python) is a collection of elements stored in a contiguous memory block. Each element is accessible by an index.

- Fixed-size in some languages (e.g., arrays in Java, C++)
- Dynamic in others (e.g., lists in Python)

---

## 🧩 Visualizing Arrays

### Memory Layout

An array stores elements in contiguous memory:

```
Index:   0   1   2   3   4
Value:   1   2   3   4   5
```

- Each element can be accessed directly by its index (O(1)).

---

## 🛠️ How to Use (Python)

```python
# Basic array (list) operations in Python
- Create a list, access elements, add/remove items, insert at a position, remove by value
arr = [1, 2, 3, 4]
print(arr[0])     # Access first element (prints 1)
arr.append(5)     # Add 5 to the end of the list
arr.pop()         # Remove the last element (removes 5)
arr.insert(2, 9)  # Insert 9 at index 2 (arr becomes [1, 2, 9, 3, 4])
arr.remove(2)     # Remove the first occurrence of 2 (arr becomes [1, 9, 3, 4])
# All operations above are O(1) except insert/remove (O(n) in worst case)

# Example:
```mermaid
graph LR
    Start[Start: 1,2,3,4]
    Append[append5]
    Pop[pop]
    Insert[insert2,9]
    Remove[remove2]
    Final[Final: 1,9,3,4]
    
    Start --> Append --> Pop --> Insert --> Remove --> Final
    
    State1[1,2,3,4,5]
    State2[1,2,3,4]
    State3[1,2,9,3,4]
    State4[1,9,3,4]
    
    Append -.-> State1
    Pop -.-> State2
    Insert -.-> State3
    Remove -.-> State4
    
    style Final fill:#99ff99
```
```

---

## 🧩 Two Sum Step-by-Step

```mermaid
graph TD
    Start["Start: nums=[2,7,11,15], target=9"]
    Step1["Step 1: i=0, num=2\ncomplement=7, seen={}"]
    Step2["Step 2: i=1, num=7\ncomplement=2, seen={2:0}\nFound pair!"]
    Result["Result: [0,1]"]

    Start --> Step1 --> Step2 --> Result
    style Result fill:#99ff99
```

## 🧩 Two Sum Visualization

Suppose nums = [2, 7, 11, 15], target = 9

| i | num | complement | seen         | Output      |
|---|-----|------------|--------------|-------------|
| 0 | 2   | 7          | {}           | -           |
| 1 | 7   | 2          | {2: 0}       | [0, 1]      |

- At i=1, complement 2 is in seen, so return [0, 1].

---

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

# Example:
nums = [2, 7, 11, 15]
target = 9
print(two_sum(nums, target))  # Output: [0, 1]
```

---

## 🧩 Kadane's Algorithm Step-by-Step

Suppose nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]

| i | num | cur_sum         | max_sum |
|---|-----|----------------|---------|
| 0 | -2  | -2             | -2      |
| 1 | 1   | max(1, -2+1)=1 | 1       |
| 2 | -3  | max(-3, 1-3)=-2| 1       |
| 3 | 4   | max(4, -2+4)=4 | 4       |
| 4 | -1  | max(-1, 4-1)=3 | 4       |
| 5 | 2   | max(2, 3+2)=5  | 5       |
| 6 | 1   | max(1, 5+1)=6  | 6       |
| 7 | -5  | max(-5, 6-5)=1 | 6       |
| 8 | 4   | max(4, 1+4)=5  | 6       |

- The maximum subarray sum is 6 (subarray [4, -1, 2, 1]).

---

## 🧩 Maximum Subarray Step-by-Step

```mermaid
graph TD
    Start["Start: nums=[-2,1,-3,4,-1,2,1,-5,4]"]
    Step1["Step 1: cur_sum=-2, max_sum=-2"]
    Step2["Step 2: cur_sum=1, max_sum=1"]
    Step3["Step 3: cur_sum=-2, max_sum=1"]
    Step4["Step 4: cur_sum=4, max_sum=4"]
    Step5["Step 5: cur_sum=3, max_sum=4"]
    Step6["Step 6: cur_sum=5, max_sum=5"]
    Step7["Step 7: cur_sum=6, max_sum=6"]
    Step8["Step 8: cur_sum=1, max_sum=6"]
    Step9["Step 9: cur_sum=5, max_sum=6"]
    Result["Result: 6 (max subarray sum)"]

    Start --> Step1 --> Step2 --> Step3 --> Step4 --> Step5 --> Step6 --> Step7 --> Step8 --> Step9 --> Result
    style Result fill:#99ff99
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

# Example:
nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
print(max_subarray(nums))  # Output: 6
```

---

## 🔁 Variants

- Rotate array
- Sliding window max/min
- Prefix sum queries
- Rearrangement (Dutch national flag, wiggle sort)

---

