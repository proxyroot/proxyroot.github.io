---
title: "Segment Tree / Fenwick Tree Overview"
date: 2024-05-12
categories: [data-structures]
tags: [segment-tree, fenwick-tree, python]
---

# Segment Tree / Fenwick Tree

## ✅ What It Is

Both **Segment Tree** and **Fenwick Tree** (aka Binary Indexed Tree) are data structures used to efficiently perform **range queries and updates**.

- Segment Tree: general-purpose, supports any associative operation (sum, min, max, gcd, etc.)
- Fenwick Tree: optimized for prefix sum queries and point updates (simpler and more space-efficient)

## 🛠️ Segment Tree (Python)

```python
class SegmentTree:
    def __init__(self, nums):
        self.n = len(nums)
        self.tree = [0] * (2 * self.n)
        for i in range(self.n):
            self.tree[self.n + i] = nums[i]
        for i in range(self.n - 1, 0, -1):
            self.tree[i] = self.tree[i * 2] + self.tree[i * 2 + 1]

    def update(self, index, value):
        i = index + self.n
        self.tree[i] = value
        while i > 1:
            i //= 2
            self.tree[i] = self.tree[2 * i] + self.tree[2 * i + 1]

    def query(self, left, right):  # inclusive range [left, right]
        result = 0
        l, r = left + self.n, right + self.n
        while l <= r:
            if l % 2 == 1:
                result += self.tree[l]
                l += 1
            if r % 2 == 0:
                result += self.tree[r]
                r -= 1
            l //= 2
            r //= 2
        return result
```

## 🛠️ Fenwick Tree (Python)

```python
class FenwickTree:
    def __init__(self, size):
        self.tree = [0] * (size + 1)

    def update(self, index, delta):
        i = index + 1
        while i < len(self.tree):
            self.tree[i] += delta
            i += i & -i

    def query(self, index):  # prefix sum [0..index]
        i = index + 1
        result = 0
        while i > 0:
            result += self.tree[i]
            i -= i & -i
        return result
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

