---
title: "Bit Manipulation Overview"
date: 2023-09-18
categories: [algorithms]
tags: [bit-manipulation, python]
---

Bit manipulation involves using bitwise operators to perform fast, low-level operations on integers. This post introduces common bit manipulation techniques, their use cases, and provides practical Python examples and problems for mastering bitwise logic.

## Overview

**Bit manipulation** involves using bitwise operators to perform operations directly on the binary representations of integers.

- Fast and memory efficient
- Common operators: `&` (AND), `|` (OR), `^` (XOR), `~` (NOT), `<<` (left shift), `>>` (right shift)

## 🛠️ Common Techniques

```python
x & 1          # Check if x is odd (last bit is 1)
x >> 1         # Divide by 2 (right shift)
x << 1         # Multiply by 2 (left shift)
x ^ y          # Flip bits where x and y differ (XOR)
x & (x - 1)    # Removes the lowest set bit
x & -x         # Isolates the lowest set bit
bin(x).count("1")  # Count set bits (Hamming weight)
# All above are O(1) bitwise operations
```

## 📦 Use Cases

- Checking power of two
- Counting set bits (Hamming weight)
- Subsets via bitmasks
- XOR properties for unique element problems

## 📘 Sample Problem 1: Single Number

> Every element appears twice except one. Find it.

```python
# This function finds the single number that appears only once using XOR.
def single_number(nums):
    result = 0
    for num in nums:
        result ^= num  # XOR cancels out pairs
    return result
# Time complexity: O(n), Space complexity: O(1)
```

## 📘 Sample Problem 2: Count Bits

> For every number i from 0 to n, return number of set bits.

```python
# This function returns a list where each element is the number of set bits in i.
def count_bits(n):
    dp = [0] * (n + 1)
    for i in range(1, n + 1):
        dp[i] = dp[i >> 1] + (i & 1)  # Use previous result and add 1 if last bit is set
    return dp
# Time complexity: O(n), Space complexity: O(n)
```

## 🔁 Variants

- Bitmask DP
- Subsets generation: `for mask in range(1 << n)`
- Gray code

---

