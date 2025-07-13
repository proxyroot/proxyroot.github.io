---
title: "Memoization Overview"
date: 2023-07-01
categories: [algorithms]
tags: [memoization, dp, dynamic-programming, top-down, optimization, recursion, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, lru-cache, problem-solving, big-o, time-complexity, tabulation, dfs, grid, word-break, fibonacci, climbing-stairs]
---

Memoization is an optimization technique that speeds up recursive algorithms by storing and reusing results of expensive function calls. It is a key part of top-down dynamic programming and is especially useful for problems with overlapping subproblems. This post explains how to use memoization, with annotated code and practical examples for beginners.

---

## 🧩 Visualizing Memoization

### Example: Fibonacci Recursion Tree (n=5)

```mermaid
graph TD;
  F5["fib(5)"] --> F4["fib(4)"];
  F5 --> F3a["fib(3)"];
  F4 --> F3b["fib(3)"];
  F4 --> F2a["fib(2)"];
  F3a --> F2b["fib(2)"];
  F3a --> F1a["fib(1)"];
  F3b --> F2c["fib(2)"];
  F3b --> F1b["fib(1)"];
  F2a --> F1c["fib(1)"];
  F2a --> F0a["fib(0)"];
  F2b --> F1d["fib(1)"];
  F2b --> F0b["fib(0)"];
  F2c --> F1e["fib(1)"];
  F2c --> F0c["fib(0)"];
```

- With memoization, repeated calls (like `fib(3)`, `fib(2)`) are cached and not recomputed.

---

## How to Use

### 1. Manual Memoization with Dictionary

```python
# Manual memoization using a dictionary
# Time Complexity: O(n), Space Complexity: O(n)
memo = {}

def fib(n):
    # Check if result is already cached
    if n in memo:
        return memo[n]
    # Base case
    if n <= 1:
        return n
    # Store result in memo before returning
    memo[n] = fib(n - 1) + fib(n - 2)
    return memo[n]

# Example usage:
print(fib(5))  # Output: 5
print(fib(10)) # Output: 55
```

- **Input:** `fib(5)`
- **Output:** `5`
- **Memoization Table after execution:** `{0: 0, 1: 1, 2: 1, 3: 2, 4: 3, 5: 5}`

### 2. Using @lru_cache

```python
from functools import lru_cache

# Memoization using Python's built-in LRU cache
@lru_cache(maxsize=None)
def fib(n):
    if n <= 1:
        return n
    return fib(n - 1) + fib(n - 2)

# Example usage:
print(fib(5))  # Output: 5
```

---

## 🧩 Climbing Stairs Visualization

Suppose n = 4. The recursion tree (with memoization) looks like:

```mermaid
graph TD;
  CS4["climb(4)"] --> CS3["climb(3)"];
  CS4 --> CS2a["climb(2)"];
  CS3 --> CS2b["climb(2)"];
  CS3 --> CS1a["climb(1)"];
  CS2a --> CS1b["climb(1)"];
  CS2a --> CS0a["climb(0)"];
  CS2b --> CS1c["climb(1)"];
  CS2b --> CS0b["climb(0)"];
```

- Each node splits into two: climb 1 step or 2 steps.
- Memoization avoids recomputing `climb(2)` and `climb(1)`.

---

## Sample Problem 1: Climbing Stairs

> You can climb 1 or 2 steps. How many distinct ways to reach the top of n steps?

```python
from functools import lru_cache

# Climbing Stairs with memoization
# Time Complexity: O(n), Space Complexity: O(n)
@lru_cache(maxsize=None)
def climb_stairs(n):
    # Base cases
    if n <= 2:
        return n
    # Recursive case: sum of ways from n-1 and n-2
    return climb_stairs(n - 1) + climb_stairs(n - 2)

# Example usage:
print(climb_stairs(4))  # Output: 5
print(climb_stairs(5))  # Output: 8
```

- **Input:** `climb_stairs(4)`
- **Output:** `5` (ways: 1-1-1-1, 1-1-2, 1-2-1, 2-1-1, 2-2)

---

## 🧩 Word Break Visualization

Suppose s = "leetcode", wordDict = ["leet", "code"]

- The function tries to break the string at every position and checks if the prefix is in the dictionary.
- Memoization avoids recomputing for the same start index.

| start | s[start:end] | in dict? | dfs(end) |
|-------|--------------|----------|----------|
| 0     | leet         | Yes      | dfs(4)   |
| 4     | code         | Yes      | dfs(8)   |
| 8     | (end)        | -        | True     |

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
        # If we've reached the end, return True
        if start == len(s):
            return True
        # If already computed, return cached result
        if start in memo:
            return memo[start]

        # Try every possible end index
        for end in range(start + 1, len(s) + 1):
            if s[start:end] in word_set and dfs(end):
                memo[start] = True
                return True
        memo[start] = False
        return False

    return dfs(0)

# Example usage:
s = "leetcode"
wordDict = ["leet", "code"]
print(word_break(s, wordDict))  # Output: True
```

- **Input:** `s = "leetcode"`, `wordDict = ["leet", "code"]`
- **Output:** `True`
- **Memoization Table after execution:** `{8: True, 4: True, 0: True}`

---

## Variants

- Tabulation (bottom-up DP)
- Two-level memoization (for grid problems)
- Memoized DFS

---

Memoization is a must-know technique for optimizing recursive solutions and dynamic programming problems. 