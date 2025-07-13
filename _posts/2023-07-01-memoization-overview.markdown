---
title: "Memoization Overview"
date: 2023-07-01
categories: [algorithms]
tags: [dp, memoization, optimization, recursion]
---

Memoization is an optimization technique that speeds up recursive algorithms by storing and reusing results of expensive function calls. It is a key part of top-down dynamic programming and is especially useful for problems with overlapping subproblems. This post explains how to use memoization, with annotated code and practical examples for beginners.

---

## What is Memoization?

Memoization is an optimization technique used to speed up recursive algorithms by storing the results of expensive function calls and reusing them when the same inputs occur again. It is commonly used with top-down dynamic programming.

---

## How to Use

### 1. Manual Memoization with Dictionary

```python
# Manual memoization using a dictionary
# Time Complexity: O(n), Space Complexity: O(n)
memo = {}

def fib(n):
    if n in memo:
        return memo[n]
    if n <= 1:
        return n
    memo[n] = fib(n - 1) + fib(n - 2)
    return memo[n]

# Example usage:
# print(fib(10))  # Output: 55
```

### 2. Using @lru_cache

```python
from functools import lru_cache

# Memoization using Python's built-in LRU cache
def fib(n):
    if n <= 1:
        return n
    return fib(n - 1) + fib(n - 2)

fib = lru_cache(maxsize=None)(fib)
# Example usage:
# print(fib(10))  # Output: 55
```

---

## Use Cases

- Fibonacci, Tribonacci
- Climbing Stairs
- Unique Paths
- Word Break, Palindrome Partitioning
- Min/Max Cost Recursions

---

## Sample Problem 1: Climbing Stairs

> You can climb 1 or 2 steps. How many distinct ways to reach the top of n steps?

```python
from functools import lru_cache

# Climbing Stairs with memoization
# Time Complexity: O(n), Space Complexity: O(n)
@lru_cache(maxsize=None)
def climb_stairs(n):
    if n <= 2:
        return n
    return climb_stairs(n - 1) + climb_stairs(n - 2)

# Example usage:
# print(climb_stairs(5))  # Output: 8
```

---

## Sample Problem 2: Word Break

> Return True if the string can be segmented into words from the dictionary.

```python
# Word Break with memoization
# Time Complexity: O(n^3), Space Complexity: O(n)
def word_break(s, wordDict):
    word_set = set(wordDict)
    memo = {}

    def dfs(start):
        if start == len(s):
            return True
        if start in memo:
            return memo[start]

        for end in range(start + 1, len(s) + 1):
            if s[start:end] in word_set and dfs(end):
                memo[start] = True
                return True
        memo[start] = False
        return False

    return dfs(0)

# Example usage:
# s = "leetcode"
# wordDict = ["leet", "code"]
# print(word_break(s, wordDict))  # Output: True
```

---

## Variants

- Tabulation (bottom-up DP)
- Two-level memoization (for grid problems)
- Memoized DFS

---

Memoization is a must-know technique for optimizing recursive solutions and dynamic programming problems. 