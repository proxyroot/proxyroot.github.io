---
title: "Greedy Algorithms Overview"
date: 2023-10-20
categories: [algorithms]
tags: [greedy, python]
---

# Greedy Algorithms

## ✅ What It Is

**Greedy algorithms** build a solution step by step by making the **locally optimal choice** at each step, hoping this leads to a global optimum.

- Greedy ≠ always optimal
- Works well when problem has **greedy-choice property** and **optimal substructure**

## 🛠️ How to Use (General Approach)

1. Sort items if needed (by weight, value, time, etc.)
2. Iterate and make the best local decision
3. Use greedy decisions to construct the final solution

## 📦 Use Cases

- Scheduling problems
- Interval problems
- Huffman encoding
- Minimum spanning trees (Kruskal’s, Prim’s)
- Dijkstra’s algorithm (with min-heap)

## 📘 Sample Problem 1: Activity Selection

> Given intervals, choose the maximum number of non-overlapping ones.

```python
def activity_selection(intervals):
    intervals.sort(key=lambda x: x[1])  # sort by end time
    count = end = 0
    for start, finish in intervals:
        if start >= end:
            count += 1
            end = finish
    return count
```

## 📘 Sample Problem 2: Jump Game

> You’re given an array where each element is max jump length. Can you reach the last index?

```python
def can_jump(nums):
    reach = 0
    for i, num in enumerate(nums):
        if i > reach:
            return False
        reach = max(reach, i + num)
    return True
```

## 🔁 Variants

- Fractional knapsack (can take fractional items)
- Greedy with lookahead (e.g. greedy + backtracking)
- Greedy + heap (e.g. scheduling)

---

