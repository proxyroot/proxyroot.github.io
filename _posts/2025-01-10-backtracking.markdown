---
title: "Backtracking"
date: 2025-01-10
categories: [algorithms]
tags: [backtracking, recursion, python]
---

Backtracking is a recursive algorithmic technique for solving constraint satisfaction problems by incrementally building solutions and abandoning paths that fail. This post explores backtracking patterns, use cases, and provides practical Python examples and problems for mastering this approach.

## ✅ What It Is

**Backtracking** is a recursive algorithmic technique for solving constraint satisfaction problems by trying to build a solution incrementally and abandoning paths that fail (backtracking).

It is a **depth-first search** in the solution space.

## 🛠️ Pattern Template

```python
# General backtracking pattern template
def backtrack(state):
    if is_goal(state):
        output.append(state[:])  # Found a valid solution, add a copy
        return

    for choice in choices(state):
        if is_valid(choice, state):
            state.append(choice)    # Make a choice
            backtrack(state)        # Explore further
            state.pop()             # Undo the choice (backtrack)
# Time complexity: depends on the problem, often exponential
```

## 📦 Use Cases

- Combinatorics (permutations, combinations)
- Puzzle solving (Sudoku, N-Queens)
- Pathfinding (maze, robot path)
- Subset generation

## 📘 Sample Problem 1: Permutations

> Return all permutations of a given list of numbers.

```python
# This function returns all permutations of a list using backtracking.
def permute(nums):
    result = []

    def backtrack(path, remaining):
        if not remaining:
            result.append(path[:])  # Found a permutation
            return
        for i in range(len(remaining)):
            backtrack(path + [remaining[i]], remaining[:i] + remaining[i+1:])  # Choose next

    backtrack([], nums)
    return result
# Time complexity: O(n!), Space complexity: O(n) for recursion stack
```

## 📘 Sample Problem 2: N-Queens

> Place N queens on an N×N chessboard so that no two queens threaten each other.

```python
# This function solves the N-Queens problem using backtracking.
def solve_n_queens(n):
    result = []

    def backtrack(row, cols, diag1, diag2, board):
        if row == n:
            result.append(["".join(r) for r in board])  # Found a valid board
            return

        for col in range(n):
            # Check if placing a queen is valid
            if col in cols or (row - col) in diag1 or (row + col) in diag2:
                continue
            board[row][col] = 'Q'  # Place queen
            backtrack(row + 1, cols | {col}, diag1 | {row - col}, diag2 | {row + col}, board)
            board[row][col] = '.'  # Remove queen (backtrack)

    board = [['.'] * n for _ in range(n)]
    backtrack(0, set(), set(), set(), board)
    return result
# Time complexity: O(n!), Space complexity: O(n^2) for board
```

## 🔁 Variants

- With pruning (early termination)
- Lexicographic or sorted outputs
- Constraint optimization (backtrack + best)

---

