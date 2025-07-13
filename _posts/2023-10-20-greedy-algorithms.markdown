---
title: "Greedy Algorithms Overview"
date: 2023-10-20
categories: [algorithms]
tags: [greedy, python]
---

Greedy algorithms build solutions step by step by making the locally optimal choice at each stage, aiming for a global optimum. This post explains the greedy approach, its use cases, and provides practical Python examples and problems for understanding greedy strategies.

## Overview

**Greedy algorithms** build a solution step by step by making the **locally optimal choice** at each step, hoping this leads to a global optimum.

- Greedy ≠ always optimal
- Works well when problem has **greedy-choice property** and **optimal substructure**

## 🧩 Visualizing Greedy Algorithms

### Greedy Choice Property

```
Problem: Select maximum non-overlapping intervals
Intervals: [(1,4), (2,6), (3,5), (5,7), (6,8)]

Greedy Strategy: Always choose the interval with earliest end time

Step 1: Choose (1,4) - earliest end time
Step 2: Choose (3,5) - next earliest end time that doesn't overlap
Step 3: Choose (5,7) - next earliest end time that doesn't overlap
Step 4: Choose (6,8) - next earliest end time that doesn't overlap

Result: 4 intervals selected
```

---

## 🛠️ How to Use (General Approach)

```python
# General greedy algorithm steps:
# 1. Sort items if needed (by weight, value, time, etc.)
# 2. Iterate and make the best local decision
# 3. Use greedy decisions to construct the final solution
# Time complexity depends on the problem (often O(n log n) due to sorting)

# Example:
def greedy_example(items):
    items.sort()  # Step 1: Sort
    result = []
    for item in items:  # Step 2: Make local choice
        if is_valid_choice(item, result):
            result.append(item)  # Step 3: Add to solution
    return result
```

---

## 🧩 Activity Selection Step-by-Step

Suppose intervals = [(1,4), (2,6), (3,5), (5,7), (6,8)]

| Step | Current Interval | End Time | Start >= End? | Count | Selected |
|------|------------------|----------|---------------|-------|----------|
| 1    | (1,4)           | 0        | 1 >= 0        | 1     | (1,4)    |
| 2    | (3,5)           | 4        | 3 >= 4        | 1     | Skip     |
| 3    | (2,6)           | 4        | 2 >= 4        | 1     | Skip     |
| 4    | (5,7)           | 4        | 5 >= 4        | 2     | (5,7)    |
| 5    | (6,8)           | 7        | 6 >= 7        | 2     | Skip     |

- Final result: 2 intervals [(1,4), (5,7)]

---

## 📘 Sample Problem 1: Activity Selection

> Given intervals, choose the maximum number of non-overlapping ones.

```python
# This function selects the maximum number of non-overlapping intervals.
def activity_selection(intervals):
    intervals.sort(key=lambda x: x[1])  # Sort by end time
    count = end = 0
    for start, finish in intervals:
        if start >= end:
            count += 1
            end = finish  # Update end to the current interval's finish
    return count
# Time complexity: O(n log n) (for sorting), Space: O(1)

# Example:
intervals = [(1,4), (2,6), (3,5), (5,7), (6,8)]
print(activity_selection(intervals))  # Output: 2
```

---

## 🧩 Jump Game Flow

Suppose nums = [2, 3, 1, 1, 4]

| i | nums[i] | reach | i > reach? | i + nums[i] | new reach | Action |
|---|---------|-------|------------|-------------|-----------|--------|
| 0 | 2       | 0     | 0 > 0      | 0 + 2 = 2   | 2         | Update |
| 1 | 3       | 2     | 1 > 2      | 1 + 3 = 4   | 4         | Update |
| 2 | 1       | 4     | 2 > 4      | 2 + 1 = 3   | 4         | Keep   |
| 3 | 1       | 4     | 3 > 4      | 3 + 1 = 4   | 4         | Keep   |
| 4 | 4       | 4     | 4 > 4      | 4 + 4 = 8   | 8         | Update |

- Can reach index 4, return True.

---

## 📘 Sample Problem 2: Jump Game

> You're given an array where each element is max jump length. Can you reach the last index?

```python
# This function checks if you can reach the last index using a greedy approach.
def can_jump(nums):
    reach = 0  # Farthest index we can reach so far
    for i, num in enumerate(nums):
        if i > reach:
            return False  # Can't reach this index
        reach = max(reach, i + num)  # Update farthest reach
    return True
# Time complexity: O(n), Space complexity: O(1)

# Example:
nums = [2, 3, 1, 1, 4]
print(can_jump(nums))  # Output: True
```

---

## 🔁 Variants

- Fractional knapsack (can take fractional items)
- Greedy with lookahead (e.g. greedy + backtracking)
- Greedy + heap (e.g. scheduling)

---

