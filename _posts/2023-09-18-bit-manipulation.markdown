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

## 🧩 Visualizing Bit Manipulation

### Binary Representation

```
Decimal: 13
Binary:  1101

Position: 3 2 1 0
Bits:     1 1 0 1
Value:    8+4+0+1 = 13
```

### Common Bitwise Operations

```
x = 13 (1101), y = 6 (0110)

x & y:  1101 & 0110 = 0100 (4)   # AND: 1 only if both bits are 1
x | y:  1101 | 0110 = 1111 (15)  # OR: 1 if either bit is 1
x ^ y:  1101 ^ 0110 = 1011 (11)  # XOR: 1 if bits are different
~x:     ~1101 = 0010 (-14)       # NOT: flip all bits
x << 1: 1101 << 1 = 11010 (26)   # Left shift: multiply by 2
x >> 1: 1101 >> 1 = 0110 (6)     # Right shift: divide by 2
```

---

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

# Example:
x = 13
print(x & 1)      # Output: 1 (odd)
print(x >> 1)     # Output: 6 (13/2)
print(x << 1)     # Output: 26 (13*2)
print(bin(x).count("1"))  # Output: 3 (1101 has 3 ones)
```

---

## 🧩 Single Number Step-by-Step

Suppose nums = [4, 1, 2, 1, 2]

| Step | num | result (binary) | result (decimal) |
|------|----|-----------------|------------------|
| 0    | -  | 0000            | 0                |
| 1    | 4  | 0100            | 4                |
| 2    | 1  | 0101            | 5                |
| 3    | 2  | 0111            | 7                |
| 4    | 1  | 0110            | 6                |
| 5    | 2  | 0100            | 4                |

- Final result: 4 (the single number).

---

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

# Example:
nums = [4, 1, 2, 1, 2]
print(single_number(nums))  # Output: 4
```

---

## 🧩 Count Bits Flow

For n = 5, the dynamic programming approach:

| i | Binary | i >> 1 | dp[i >> 1] | i & 1 | dp[i] |
|---|--------|--------|------------|-------|-------|
| 0 | 000    | -      | -          | -     | 0     |
| 1 | 001    | 000    | 0          | 1     | 1     |
| 2 | 010    | 001    | 1          | 0     | 1     |
| 3 | 011    | 001    | 1          | 1     | 2     |
| 4 | 100    | 010    | 1          | 0     | 1     |
| 5 | 101    | 010    | 1          | 1     | 2     |

- Result: [0, 1, 1, 2, 1, 2]

---

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

# Example:
n = 5
print(count_bits(n))  # Output: [0, 1, 1, 2, 1, 2]
```

---

## 🔁 Variants

- Bitmask DP
- Subsets generation: `for mask in range(1 << n)`
- Gray code

---

