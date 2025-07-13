---
title: "Segment Tree / Fenwick Tree Overview"
date: 2024-05-12
categories: [data-structures]
tags: [segment-tree, fenwick-tree, binary-indexed-tree, range-query, data-structures, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, prefix-sum, time-series, competitive-programming, problem-solving, big-o, time-complexity, range-update, lazy-propagation]
---

Segment Trees and Fenwick Trees (Binary Indexed Trees) are advanced data structures for efficient range queries and updates. This post explains their differences, use cases, and provides practical Python implementations and problems for mastering these structures.

## Overview

Both **Segment Tree** and **Fenwick Tree** (aka Binary Indexed Tree) are data structures used to efficiently perform **range queries and updates**.

- Segment Tree: general-purpose, supports any associative operation (sum, min, max, gcd, etc.)
- Fenwick Tree: optimized for prefix sum queries and point updates (simpler and more space-efficient)

## 🧩 Visualizing a Segment Tree

Suppose we have an input array:

```python
nums = [2, 1, 5, 3]
```

The segment tree for range sum will look like this (as a binary tree):

```mermaid
graph TD
    A["[0,3] = 11"] --> B["[0,1] = 3"]
    A --> C["[2,3] = 8"]
    B --> D["[0,0] = 2"]
    B --> E["[1,1] = 1"]
    C --> F["[2,2] = 5"]
    C --> G["[3,3] = 3"]
```

- Each node stores the sum of a range.
- The root covers the whole array, children cover subranges.

The internal array (`self.tree`) after building will be:

```
Index: 0 1 2 3 4 5 6 7
Value: x 11  3  8 2 1 5 3
```
- `self.tree[1]` is the sum of all elements (11)
- `self.tree[2]` is sum of [2,1] (3)
- `self.tree[3]` is sum of [5,3] (8)
- `self.tree[4]` is 2, `self.tree[5]` is 1, etc.

## 🛠️ Segment Tree

```python
# Implementation of a Segment Tree for range sum queries and point updates
class SegmentTree:
    def __init__(self, nums):
        self.n = len(nums)
        # The tree is a flat array of size 2*n
        self.tree = [0] * (2 * self.n)
        # Insert leaf nodes in the second half
        for i in range(self.n):
            self.tree[self.n + i] = nums[i]  # Leaves: tree[n] to tree[2n-1]
        # Build the internal nodes by combining children
        for i in range(self.n - 1, 0, -1):
            self.tree[i] = self.tree[i * 2] + self.tree[i * 2 + 1]
            # Each parent is the sum of its two children

    def update(self, index, value):
        # Update value at index and rebuild tree upwards
        i = index + self.n  # Move to the leaf
        self.tree[i] = value
        while i > 1:
            i //= 2  # Move to parent
            self.tree[i] = self.tree[2 * i] + self.tree[2 * i + 1]  # Update parent

    def query(self, left, right):  # inclusive range [left, right]
        result = 0
        l, r = left + self.n, right + self.n  # Move to leaves
        while l <= r:
            if l % 2 == 1:
                result += self.tree[l]  # If l is a right child
                l += 1
            if r % 2 == 0:
                result += self.tree[r]  # If r is a left child
                r -= 1
            l //= 2
            r //= 2
        return result
# Time complexity: O(log n) for update/query, Space: O(n)
```

## 🧩 Visualizing a Fenwick Tree (Binary Indexed Tree)

For the same input:

```python
nums = [2, 1, 5, 3]
```

The Fenwick tree's internal array after building (using repeated updates):

```
Index: 0 1 2 3 4
Value: x 2 3 5 11
```
- `tree[1]` = 2
- `tree[2]` = 2+1 = 3
- `tree[3]` = 5
- `tree[4]` = 2+1+5+3 = 11

## 🛠️ Fenwick Tree

```python
# Implementation of a Fenwick Tree (Binary Indexed Tree) for prefix sums
class FenwickTree:
    def __init__(self, size):
        # tree[0] is unused; tree[1..size] holds the data
        self.tree = [0] * (size + 1)

    def update(self, index, delta):
        # Add delta to index and all relevant ancestors
        i = index + 1  # Fenwick tree is 1-indexed internally
        while i < len(self.tree):
            self.tree[i] += delta
            i += i & -i  # Move to next responsible node

    def query(self, index):  # prefix sum [0..index]
        i = index + 1
        result = 0
        while i > 0:
            result += self.tree[i]
            i -= i & -i  # Move to parent
        return result
# Time complexity: O(log n) for update/query, Space: O(n)
```

## 📦 Use Cases

- Range sum / min / max queries
- Time series analysis
- 2D matrix queries (extended versions)
- Competitive programming / segment-based DP

## 📘 Sample Problem 1: Range Sum Query

> Given an array, support sum queries in a range and single index updates efficiently.

## 📘 Sample Problem 2: Count of Smaller Numbers After Self

> For each number, count how many smaller numbers appear to its right (can use Fenwick tree with discretization).

## 🔁 Variants

- Lazy Segment Tree (for range updates)
- 2D Fenwick Tree (matrix prefix sums)
- Range add & range query via dual BIT

---

