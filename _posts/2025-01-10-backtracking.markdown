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
def backtrack(state):
    if is_goal(state):
        output.append(state[:])
        return

    for choice in choices(state):
        if is_valid(choice, state):
            state.append(choice)
            backtrack(state)
            state.pop()
```

## 📦 Use Cases

- Combinatorics (permutations, combinations)
- Puzzle solving (Sudoku, N-Queens)
- Pathfinding (maze, robot path)
- Subset generation

## 📘 Sample Problem 1: Permutations

> Return all permutations of a given list of numbers.

```python
def permute(nums):
    result = []

    def backtrack(path, remaining):
        if not remaining:
            result.append(path[:])
            return
        for i in range(len(remaining)):
            backtrack(path + [remaining[i]], remaining[:i] + remaining[i+1:])

    backtrack([], nums)
    return result
```

## 📘 Sample Problem 2: N-Queens

> Place N queens on an N×N chessboard so that no two queens threaten each other.

```python
def solve_n_queens(n):
    result = []

    def backtrack(row, cols, diag1, diag2, board):
        if row == n:
            result.append(["".join(r) for r in board])
            return

        for col in range(n):
            if col in cols or (row - col) in diag1 or (row + col) in diag2:
                continue
            board[row][col] = 'Q'
            backtrack(row + 1, cols | {col}, diag1 | {row - col}, diag2 | {row + col}, board)
            board[row][col] = '.'

    board = [['.'] * n for _ in range(n)]
    backtrack(0, set(), set(), set(), board)
    return result
```

## 🔁 Variants

- With pruning (early termination)
- Lexicographic or sorted outputs
- Constraint optimization (backtrack + best)

---

