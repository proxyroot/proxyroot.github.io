---
title: "Stack Overview"
date: 2023-10-01
categories: [data-structures]
tags: [stack, stacks, data-structures, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, LIFO, problem-solving, big-o, time-complexity, parsing, recursion]
---

A stack is a Last-In-First-Out (LIFO) data structure used for managing data with push and pop operations. This post covers stack fundamentals, typical use cases, and practical Python examples, including common problems and variations.

# Stack

## Overview

A **stack** is a Last-In-First-Out (LIFO) data structure.

- Elements are added/removed from the **top** only
- Think: stack of plates 🍽️

In Python, use `list` or `collections.deque`.

## 🛠️ How to Use (Python)

```python
# Basic stack operations in Python
# - Use a list to push, pop, peek, and check if empty
stack = []
stack.append(1)    # Push 1 onto the stack
stack.append(2)    # Push 2 onto the stack
stack.pop()        # Pop the top element (returns 2)
stack[-1]          # Peek at the top element (returns 1)
not stack          # Check if the stack is empty (returns False)
# All stack operations above are O(1)
```

## 📦 Use Cases

- Undo functionality
- Backtracking (e.g., maze solving, recursion)
- Parsing (e.g., parentheses validation)
- Tree/graph traversal (iterative DFS)

## 📘 Sample Problem 1: Valid Parentheses

> Given a string of brackets, return true if they are validly nested.

```python
# This function checks if a string of brackets is validly nested using a stack.
def is_valid(s):
    stack = []
    mapping = {')': '(', ']': '[', '}': '{'}  # Map closing to opening brackets
    for char in s:
        if char in mapping.values():
            stack.append(char)  # Push opening bracket
        elif not stack or mapping[char] != stack.pop():
            return False  # Mismatch or stack empty
    return not stack  # True if all brackets matched
# Time complexity: O(n), Space complexity: O(n)
```

## 📘 Sample Problem 2: Daily Temperatures

> Return an array where result[i] is the number of days until a warmer temperature.

```python
# This function returns, for each day, how many days until a warmer temperature.
def daily_temperatures(temps):
    res = [0] * len(temps)
    stack = []  # Stack to store indices of unresolved days
    for i, temp in enumerate(temps):
        while stack and temps[i] > temps[stack[-1]]:
            prev = stack.pop()
            res[prev] = i - prev  # Calculate days waited
        stack.append(i)
    return res
# Time complexity: O(n), Space complexity: O(n)
```

## 🔁 Variants

- Min Stack (track minimum value)
- Stack with two queues
- Stack for expression evaluation (RPN)

---

