---
title: "Recursion Overview"
date: 2023-07-02
categories: [algorithms]
tags: [recursion, dp, algorithms, basics]
---

Recursion is a fundamental programming technique where a function calls itself to solve smaller subproblems. It is widely used in algorithms, especially for problems that can be broken down into similar subproblems. This post covers the basics of recursion, common patterns, and annotated code examples for beginners.

---

## What is Recursion?

Recursion is when a function calls itself to solve smaller subproblems. It's used when a problem can be divided into similar subproblems.

- Every recursive function has:
  - Base case (to stop recursion)
  - Recursive case (to break into smaller parts)

---

## Basic Template

```python
# Basic recursion template
def recurse(params):
    if base_case_condition:
        return base_result
    # Recursive step: solve a smaller problem
    result = recurse(smaller_problem)
    return combine(result)
```

---

## Use Cases

- Tree traversal
- Combinatorics (factorial, subsets)
- Divide & Conquer
- Backtracking

---

## Sample Problem 1: Factorial

```python
# Recursive factorial
# Time Complexity: O(n), Space Complexity: O(n)
def factorial(n):
    if n == 0 or n == 1:
        return 1
    return n * factorial(n - 1)

# Example usage:
# print(factorial(5))  # Output: 120
```

---

## Sample Problem 2: Fibonacci

```python
# Recursive Fibonacci (naive)
# Time Complexity: O(2^n), Space Complexity: O(n)
def fib(n):
    if n <= 1:
        return n
    return fib(n - 1) + fib(n - 2)

# Example usage:
# print(fib(5))  # Output: 5
# Note: This is exponential. Use memoization or DP for optimization.
```

---

## Sample Problem 3: Binary Tree Inorder Traversal

```python
# Inorder traversal of a binary tree
# Time Complexity: O(n), Space Complexity: O(n)
def inorder(node):
    if not node:
        return []
    return inorder(node.left) + [node.val] + inorder(node.right)

# Example usage:
# class Node:
#     def __init__(self, val, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
# root = Node(1, Node(2), Node(3))
# print(inorder(root))  # Output: [2, 1, 3]
```

---

## Variants

- Tail recursion (optimized by some languages)
- Mutual recursion (two functions calling each other)
- Recursive DFS / Backtracking

---

Recursion is a must-know tool for solving problems that can be broken down into smaller, similar subproblems. 