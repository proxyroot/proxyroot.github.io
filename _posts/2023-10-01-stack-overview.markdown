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

---

## 🧩 Visualizing Stacks

### Stack Operations (LIFO)

```
Initial: []
Push 1:  [1]
Push 2:  [1, 2]
Push 3:  [1, 2, 3]
Pop:     [1, 2]     (returns 3)
Pop:     [1]         (returns 2)
Peek:    [1]         (returns 1)
```

- Last element pushed is the first one popped (LIFO).

---

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

# Example:
stack = []
stack.append(10)
stack.append(20)
print(stack.pop())  # Output: 20
print(stack[-1])    # Output: 10
```

---

## 🧩 Valid Parentheses Step-by-Step

Suppose s = "()[]{}"

| Step | char | char in values? | stack | Action           |
|------|----|-----------------|-------|------------------|
| 1    | (  | Yes            | ['('] | Push '('         |
| 2    | )  | No             | []    | Pop and match    |
| 3    | [  | Yes            | ['['] | Push '['         |
| 4    | ]  | No             | []    | Pop and match    |
| 5    | {  | Yes            | ['{'] | Push '{'         |
| 6    | }  | No             | []    | Pop and match    |

- Final stack is empty, return True.

---

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

# Example:
s = "()[]{}"
print(is_valid(s))  # Output: True
```

---

## 🧩 Daily Temperatures Flow

Suppose temps = [73, 74, 75, 71, 69, 72, 76, 73]

| i | temp | stack | temps[stack[-1]] | temp > temps[stack[-1]]? | Action           | res[prev] |
|---|----|-------|------------------|-------------------------|------------------|-----------|
| 0 | 73 | [0]   | -                | -                       | Push 0           | -         |
| 1 | 74 | []    | 73               | 74 > 73                 | Pop 0, res[0]=1  | 1         |
| 1 | 74 | [1]   | -                | -                       | Push 1           | -         |
| 2 | 75 | []    | 74               | 75 > 74                 | Pop 1, res[1]=1  | 1         |
| 2 | 75 | [2]   | -                | -                       | Push 2           | -         |
| 3 | 71 | [2,3] | 75               | 71 < 75                 | Push 3           | -         |
| 4 | 69 | [2,3,4]| 71              | 69 < 71                 | Push 4           | -         |
| 5 | 72 | [2,5] | 69               | 72 > 69                 | Pop 4, res[4]=1  | 1         |
| 5 | 72 | [2,5] | 71               | 72 > 71                 | Pop 3, res[3]=2  | 2         |
| 5 | 72 | [2,5] | 75               | 72 < 75                 | Push 5           | -         |
| 6 | 76 | [6]   | 72               | 76 > 72                 | Pop 5, res[5]=1  | 1         |
| 6 | 76 | [6]   | 75               | 76 > 75                 | Pop 2, res[2]=4  | 4         |
| 6 | 76 | [6]   | -                | -                       | Push 6           | -         |
| 7 | 73 | [6,7] | 76               | 73 < 76                 | Push 7           | -         |

- Result: [1, 1, 4, 2, 1, 1, 0, 0]

---

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

# Example:
temps = [73, 74, 75, 71, 69, 72, 76, 73]
print(daily_temperatures(temps))  # Output: [1, 1, 4, 2, 1, 1, 0, 0]
```

---

## 🔁 Variants

- Min Stack (track minimum value)
- Stack with two queues
- Stack for expression evaluation (RPN)

---

