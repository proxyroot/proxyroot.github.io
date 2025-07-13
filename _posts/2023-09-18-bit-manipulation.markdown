---
title: "Bit Manipulation Overview"
date: 2023-09-18
categories: [algorithms]
tags: [bit-manipulation, python]
---

Bit manipulation involves using bitwise operators to perform fast, low-level operations on integers. This post introduces common bit manipulation techniques, their use cases, and provides practical Python examples and problems for mastering bitwise logic.

## ✅ What It Is

**Bit manipulation** involves using bitwise operators to perform operations directly on the binary representations of integers.

- Fast and memory efficient
- Common operators: `&` (AND), `|` (OR), `^` (XOR), `~` (NOT), `<<` (left shift), `>>` (right shift)

## 🛠️ Common Techniques

```python
x & 1          # check if x is odd
x >> 1         # divide by 2
x << 1         # multiply by 2
x ^ y          # flip bits where x and y differ
x & (x - 1)    # removes the lowest set bit
x & -x         # isolates the lowest set bit
bin(x).count("1")  # count set bits
```

## 📦 Use Cases

- Checking power of two
- Counting set bits (Hamming weight)
- Subsets via bitmasks
- XOR properties for unique element problems

## 📘 Sample Problem 1: Single Number

> Every element appears twice except one. Find it.

```python
def single_number(nums):
    result = 0
    for num in nums:
        result ^= num
    return result
```

## 📘 Sample Problem 2: Count Bits

> For every number i from 0 to n, return number of set bits.

```python
def count_bits(n):
    dp = [0] * (n + 1)
    for i in range(1, n + 1):
        dp[i] = dp[i >> 1] + (i & 1)
    return dp
```

## 🔁 Variants

- Bitmask DP
- Subsets generation: `for mask in range(1 << n)`
- Gray code

---

