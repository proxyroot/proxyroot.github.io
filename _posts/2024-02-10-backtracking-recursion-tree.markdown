---
title: "Backtracking / Recursion Tree"
date: 2024-02-10
categories: [algorithms]
tags: [backtracking, recursion, python]
---

# Backtracking / Recursion Tree

## ✅ What It Is

**Backtracking** is a technique for solving problems incrementally by exploring all potential options and abandoning (“backtracking”) ones that fail.

- Often visualized as a **recursion tree**
- Brute force with pruning
- Used when problem involves **decision trees** (choose/not choose)

## 🛠️ How to Use (Python)

```python
# Subsets example

def subsets(nums):
    res = []

    def backtrack(start, path):
        res.append(path[:])
        for i in range(start, len(nums)):
            path.append(nums[i])
            backtrack(i + 1, path)
            path.pop()

    backtrack(0, [])
    return res
```

## 📦 Use Cases

- Generating permutations/combinations/subsets
- N-Queens problem
- Sudoku solver
- Word search

## 📘 Sample Problem 1: Permutations

> Return all permutations of a list of numbers.

```python
def permute(nums):
    res = []
    def backtrack(path, used):
        if len(path) == len(nums):
            res.append(path[:])
            return
        for i in range(len(nums)):
            if used[i]: continue
            used[i] = True
            path.append(nums[i])
            backtrack(path, used)
            path.pop()
            used[i] = False

    backtrack([], [False] * len(nums))
    return res
```

## 📘 Sample Problem 2: N-Queens

> Place N queens on an N×N board such that no two queens attack each other.

```python
def solve_n_queens(n):
    res = []
    board = ["." * n for _ in range(n)]

    def backtrack(r, cols, diags1, diags2):
        if r == n:
            res.append(board[:])
            return
        for c in range(n):
            if c in cols or r - c in diags1 or r + c in diags2:
                continue
            board[r] = board[r][:c] + "Q" + board[r][c+1:]
            backtrack(r+1, cols | {c}, diags1 | {r - c}, diags2 | {r + c})
            board[r] = board[r][:c] + "." + board[r][c+1:]

    backtrack(0, set(), set(), set())
    return res
```

## 🔁 Variants

- With pruning (e.g. early return if invalid)
- Lexicographic backtracking
- Bitmask-based recursion for optimization

---

