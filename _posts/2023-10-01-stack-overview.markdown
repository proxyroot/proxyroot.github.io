---
title: "Stack Overview"
date: 2023-10-01
categories: [data-structures]
tags: [stack, python]
---

# Stack

## ✅ What It Is

A **stack** is a Last-In-First-Out (LIFO) data structure.

- Elements are added/removed from the **top** only
- Think: stack of plates 🍽️

In Python, use `list` or `collections.deque`.

## 🛠️ How to Use (Python)

```python
stack = []
stack.append(1)    # push
stack.append(2)
stack.pop()        # pop → 2
stack[-1]          # peek → 1
not stack          # check if empty
```

## 📦 Use Cases

- Undo functionality
- Backtracking (e.g., maze solving, recursion)
- Parsing (e.g., parentheses validation)
- Tree/graph traversal (iterative DFS)

## 📘 Sample Problem 1: Valid Parentheses

> Given a string of brackets, return true if they are validly nested.

```python
def is_valid(s):
    stack = []
    mapping = {')': '(', ']': '[', '}': '{'}
    for char in s:
        if char in mapping.values():
            stack.append(char)
        elif not stack or mapping[char] != stack.pop():
            return False
    return not stack
```

## 📘 Sample Problem 2: Daily Temperatures

> Return an array where result[i] is the number of days until a warmer temperature.

```python
def daily_temperatures(temps):
    res = [0] * len(temps)
    stack = []  # stores indices
    for i, temp in enumerate(temps):
        while stack and temps[i] > temps[stack[-1]]:
            prev = stack.pop()
            res[prev] = i - prev
        stack.append(i)
    return res
```

## 🔁 Variants

- Min Stack (track minimum value)
- Stack with two queues
- Stack for expression evaluation (RPN)

---

