---
title: "Segment Tree / Fenwick Tree"
date: 2025-03-15
categories: [data-structures]
tags: [segment-tree, fenwick-tree, python]
---

Segment Trees and Fenwick Trees (Binary Indexed Trees) are advanced data structures for efficient range queries and point updates. This post compares their features, explains their use cases, and provides practical Python implementations and problems for mastering these structures.

## ✅ What It Is

**Segment Tree** and **Fenwick Tree (Binary Indexed Tree)** are advanced data structures used for efficient **range queries and point updates**.

| Feature           | Segment Tree             | Fenwick Tree           |
| ----------------- | ------------------------ | ---------------------- |
| Space Complexity  | O(2N)                    | O(N)                   |
| Query Complexity | O(log N)                 | O(log N)               |
| Update Complexity | O(log N)                 | O(log N)               |
| Use Cases         | Flexible (min, max, GCD) | Mostly for prefix sums |

## 🛠️ Segment Tree (Python)

```python
# Implementation of a Segment Tree for range sum queries and point updates
class SegmentTree:
    def __init__(self, nums):
        self.n = len(nums)
        self.tree = [0] * (2 * self.n)
        for i in range(self.n):
            self.tree[self.n + i] = nums[i]  # Insert leaves
        for i in range(self.n - 1, 0, -1):
            self.tree[i] = self.tree[2 * i] + self.tree[2 * i + 1]  # Build tree

    def update(self, index, value):
        index += self.n
        self.tree[index] = value  # Update leaf
        while index > 1:
            index //= 2
            self.tree[index] = self.tree[2 * index] + self.tree[2 * index + 1]  # Update parents

    def query(self, left, right):  # [left, right)
        result = 0
        left += self.n
        right += self.n
        while left < right:
            if left % 2:
                result += self.tree[left]
                left += 1
            if right % 2:
                right -= 1
                result += self.tree[right]
            left //= 2
            right //= 2
        return result
# Time complexity: O(log n) for update/query, Space: O(n)
```

## 🛠️ Fenwick Tree (Python)

```python
# Implementation of a Fenwick Tree (Binary Indexed Tree) for prefix sums
class FenwickTree:
    def __init__(self, size):
        self.tree = [0] * (size + 1)

    def update(self, index, delta):
        index += 1
        while index < len(self.tree):
            self.tree[index] += delta  # Add delta to index
            index += index & -index    # Move to parent

    def query(self, index):
        index += 1
        result = 0
        while index:
            result += self.tree[index]  # Add value at index
            index -= index & -index     # Move to parent
        return result

    def range_query(self, left, right):
        return self.query(right) - self.query(left - 1)
# Time complexity: O(log n) for update/query, Space: O(n)
```

## 📦 Use Cases

- Range sum/min/max queries
- Inversion count
- Histogram area problems
- 2D range updates (advanced)

## 📘 Sample Problem 1: Range Sum Query

> Given an array, support updating an element and getting the sum of a range. ✅ Solvable using either Segment Tree or Fenwick Tree.

## 📘 Sample Problem 2: Count of Smaller Numbers After Self

> Given an integer array, return count of smaller numbers to the right for each element.

📌 Fenwick Tree (or BST) with coordinate compression works well.

## 🔁 Variants

- Lazy propagation in Segment Tree (for range updates)
- 2D Fenwick Tree
- RMQ, GCD queries

---

