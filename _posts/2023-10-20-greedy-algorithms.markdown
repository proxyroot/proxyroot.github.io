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

## 🛠️ How to Use (General Approach)

```python
# General greedy algorithm steps:
# 1. Sort items if needed (by weight, value, time, etc.)
# 2. Iterate and make the best local decision
# 3. Use greedy decisions to construct the final solution
# Time complexity depends on the problem (often O(n log n) due to sorting)
```

## 📦 Use Cases

- Scheduling problems
- Interval problems
- Huffman encoding
- Minimum spanning trees (Kruskal’s, Prim’s)
- Dijkstra’s algorithm (with min-heap)

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
```

## 📘 Sample Problem 2: Jump Game

> You’re given an array where each element is max jump length. Can you reach the last index?

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
```

## 🔁 Variants

- Fractional knapsack (can take fractional items)
- Greedy with lookahead (e.g. greedy + backtracking)
- Greedy + heap (e.g. scheduling)

---

