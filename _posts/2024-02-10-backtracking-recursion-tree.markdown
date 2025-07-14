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

### Step-by-Step Explanation

**Goal:** Place N queens on an N×N chessboard so that no two queens threaten each other (no two in the same row, column, or diagonal).

**How Backtracking Works:**
- Start from the first row.
- Try placing a queen in each column of the current row.
- If the position is safe (not attacked by any other queen), place the queen and move to the next row.
- If you reach the last row, you found a solution!
- If you can’t place a queen in any column, backtrack (remove the last queen and try a new position).

---

### Visualizing the Recursion Tree (n=4)

```mermaid
flowchart TD
    Start([Row 0]) --> Q0C0["Place Q at (0,0)"]
    Start --> Q0C1["Place Q at (0,1)"]
    Start --> Q0C2["Place Q at (0,2)"]
    Start --> Q0C3["Place Q at (0,3)"]

    Q0C0 --> Q1C0X["(1,0) X"]
    Q0C0 --> Q1C1X["(1,1) X"]
    Q0C0 --> Q1C2["Place Q at (1,2)"]
    Q0C0 --> Q1C3X["(1,3) X"]

    Q1C2 --> Q2C0X["(2,0) X"]
    Q1C2 --> Q2C1["Place Q at (2,1)"]
    Q1C2 --> Q2C2X["(2,2) X"]
    Q1C2 --> Q2C3X["(2,3) X"]

    Q2C1 --> Q3C0X["(3,0) X"]
    Q2C1 --> Q3C1X["(3,1) X"]
    Q2C1 --> Q3C2X["(3,2) X"]
    Q2C1 --> Q3C3["Place Q at (3,3) ✔"]

    Q0C2 --> Q1C0["Place Q at (1,0)"]
    Q1C0 --> Q2C1X["(2,1) X"]
    Q1C0 --> Q2C2X["(2,2) X"]
    Q1C0 --> Q2C3["Place Q at (2,3)"]
    Q2C3 --> Q3C0["Place Q at (3,1) ✔"]

    %% All other branches end in X (invalid)
```

- Each node shows a queen placement or an invalid move (X).
- ✔ means a valid solution is found.
- The tree shows how the algorithm tries, fails, and backtracks.

---

### Annotated Code

```python
def solve_n_queens(n):
    res = []
    board = ["." * n for _ in range(n)]

    def backtrack(r, cols, diags1, diags2):
        # If all rows are filled, add solution
        if r == n:
            res.append(board[:])
            return
        for c in range(n):
            # Check if column or diagonals are attacked
            if c in cols or r - c in diags1 or r + c in diags2:
                continue
            # Place queen
            board[r] = board[r][:c] + "Q" + board[r][c+1:]
            # Mark column and diagonals as attacked
            backtrack(r+1, cols | {c}, diags1 | {r - c}, diags2 | {r + c})
            # Remove queen (backtrack)
            board[r] = board[r][:c] + "." + board[r][c+1:]

    backtrack(0, set(), set(), set())
    return res
```
- `cols` tracks columns with queens.
- `diags1` and `diags2` track diagonals.
- The function tries every column in the current row, skips if attacked, and backtracks if stuck.

---

### Analogy

Think of it like seating guests at a dinner table:
- Each guest (queen) must have their own seat (column) and not be able to “see” (attack) another guest diagonally.
- If you can’t seat a guest, you go back and try a different arrangement.


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

