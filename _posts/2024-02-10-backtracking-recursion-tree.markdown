---
title: "Backtracking / Recursion Tree"
date: 2024-02-10
categories: [algorithms]
tags: [backtracking, recursion, python]
---

Backtracking is a problem-solving technique that incrementally builds candidates and abandons those that fail constraints, often visualized as a recursion tree. This post explains backtracking, its use cases, and provides practical Python examples and problems for mastering recursive search strategies.

## Overview

**Backtracking** is a technique for solving problems incrementally by exploring all potential options and abandoning (“backtracking”) ones that fail.

- Often visualized as a **recursion tree**
- Brute force with pruning
- Used when problem involves **decision trees** (choose/not choose)

## 🧩 Visualizing Backtracking: Recursion Tree

### Example: Subsets of [1, 2, 3]

```mermaid
graph TD;
  Start["start=0, path=[]"] --> A["start=0, path=[1]"];
  Start --> B["start=1, path=[2]"];
  Start --> C["start=2, path=[3]"];
  Start --> D["start=3, path=[]"];
  A --> E["start=1, path=[1,2]"];
  A --> F["start=2, path=[1,3]"];
  A --> G["start=3, path=[1]"];
  B --> H["start=2, path=[2,3]"];
  B --> I["start=3, path=[2]"];
  C --> J["start=3, path=[3]"];
  E --> K["start=2, path=[1,2,3]"];
  E --> L["start=3, path=[1,2]"];
  F --> M["start=3, path=[1,3]"];
  H --> N["start=3, path=[2,3]"];
  K --> O["start=3, path=[1,2,3]"];
```

- Each node represents a state (start index, current path).
- Leaf nodes are the final subsets: [], [1], [2], [3], [1,2], [1,3], [2,3], [1,2,3].

---

## 🛠️ How to Use (Python)

```python
# Subsets example using backtracking
def subsets(nums):
    res = []

    def backtrack(start, path):
        res.append(path[:])  # Add current subset
        for i in range(start, len(nums)):
            path.append(nums[i])         # Choose
            backtrack(i + 1, path)       # Explore
            path.pop()                   # Un-choose (backtrack)

    backtrack(0, [])
    return res
# Time complexity: O(2^n), Space complexity: O(n) for recursion stack

# Example:
nums = [1, 2, 3]
print(subsets(nums))  # Output: [[], [1], [1, 2], [1, 2, 3], [1, 3], [2], [2, 3], [3]]
```

---

## 🧩 Permutations Step-by-Step

Suppose nums = [1, 2, 3]. The backtracking process:

| Step | path | used        | i | Action           | Result |
|------|------|-------------|---|------------------|--------|
| 1    | []   | [F,F,F]     | 0 | Add 1            | [1]    |
| 2    | [1]  | [T,F,F]     | 1 | Add 2            | [1,2]  |
| 3    | [1,2]| [T,T,F]     | 2 | Add 3            | [1,2,3]|
| 4    | [1,2,3]| [T,T,T] | - | Complete         | -      |
| 5    | [1,2]| [T,T,F]     | - | Backtrack        | -      |
| 6    | [1]  | [T,F,F]     | 2 | Add 3            | [1,3]  |
| 7    | [1,3]| [T,F,T]     | 1 | Add 2            | [1,3,2]|
| ...  | ...  | ...         | ...| ...              | ...    |

---

## 📘 Sample Problem 1: Permutations

> Return all permutations of a list of numbers.

```python
# This function returns all permutations of a list using backtracking.
def permute(nums):
    res = []
    def backtrack(path, used):
        # If path is complete, add to result
        if len(path) == len(nums):
            res.append(path[:])
            return
        # Try each number as the next element
        for i in range(len(nums)):
            if used[i]: continue  # Skip if already used
            used[i] = True        # Mark as used
            path.append(nums[i])  # Add to path
            backtrack(path, used) # Recurse
            path.pop()            # Remove from path
            used[i] = False       # Mark as unused

    backtrack([], [False] * len(nums))
    return res
# Time complexity: O(n!), Space complexity: O(n) for recursion stack

# Example:
nums = [1, 2, 3]
print(permute(nums))  # Output: [[1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,1,2], [3,2,1]]
```

---

## 🧩 N-Queens Visualization

For n = 4, one valid solution:

```
. Q . .
. . . Q
Q . . .
. . Q .
```

- Queens are placed so no two attack each other.
- Each row, column, and diagonal can have at most one queen.

---

## 📘 Sample Problem 2: N-Queens

> Place N queens on an N×N board such that no two queens attack each other.

```python
# This function solves the N-Queens problem using backtracking.
def solve_n_queens(n):
    res = []
    board = ["." * n for _ in range(n)]

    def backtrack(r, cols, diags1, diags2):
        # If all rows are filled, add solution
        if r == n:
            res.append(board[:])
            return
        # Try placing queen in each column of current row
        for c in range(n):
            # Check if position is valid
            if c in cols or r - c in diags1 or r + c in diags2:
                continue
            # Place queen
            board[r] = board[r][:c] + "Q" + board[r][c+1:]
            # Recurse with updated constraints
            backtrack(r+1, cols | {c}, diags1 | {r - c}, diags2 | {r + c})
            # Remove queen (backtrack)
            board[r] = board[r][:c] + "." + board[r][c+1:]

    backtrack(0, set(), set(), set())
    return res
# Time complexity: O(n!), Space complexity: O(n^2) for board

# Example:
n = 4
print(solve_n_queens(n))  # Output: [['.Q..', '...Q', 'Q...', '..Q.'], ['..Q.', 'Q...', '...Q', '.Q..']]
```

---

## 🔁 Variants

- With pruning (e.g. early return if invalid)
- Lexicographic backtracking
- Bitmask-based recursion for optimization

---

