---
title: "Queue / Deque Overview"
date: 2023-11-12
categories: [data-structures]
tags: [queue, deque, queues, deques, data-structures, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, FIFO, LRU, sliding-window, problem-solving, big-o, time-complexity]
---

A queue is a First-In-First-Out (FIFO) data structure, while a deque allows adding and removing elements from both ends efficiently. This post explores queues and deques in Python, their operations, use cases, and provides practical examples and problems.

## Overview

A **queue** is a First-In-First-Out (FIFO) data structure. A **deque** (double-ended queue) allows adding/removing from both ends efficiently.

In Python:

- Use `collections.deque` for both queue and deque behavior
- Use `queue.Queue` for thread-safe queues (multithreading)

## 🛠️ How to Use (Python)

```python
# Basic queue and deque operations in Python
# - Use collections.deque for efficient queue and deque behavior
from collections import deque

q = deque()
q.append(1)         # Enqueue 1 at the end
q.popleft()         # Dequeue from the front
q.appendleft(2)     # Add 2 to the front
q.pop()             # Remove from the end
# All operations above are O(1)
```

## 📦 Use Cases

- Task scheduling
- BFS (breadth-first search)
- LRU cache
- Sliding window problems

## 📘 Sample Problem 1: Moving Average from Data Stream

> Design a class that finds the moving average of the last `k` elements.

```python
from collections import deque

# This class maintains a moving average of the last k elements.
class MovingAverage:
    def __init__(self, size):
        self.q = deque()  # Store the last k elements
        self.k = size
        self.sum = 0      # Keep running sum

    def next(self, val):
        self.q.append(val)
        self.sum += val
        if len(self.q) > self.k:
            self.sum -= self.q.popleft()  # Remove oldest if over size
        return self.sum / len(self.q)     # Return current average
# Time complexity: O(1) per operation, Space complexity: O(k)
```

## 📘 Sample Problem 2: Sliding Window Maximum

> Given an array `nums` and window size `k`, return max of each window.

```python
from collections import deque

# This function finds the maximum in each sliding window of size k.
def max_sliding_window(nums, k):
    q = deque()  # Store indices of useful elements
    res = []
    for i, n in enumerate(nums):
        while q and nums[q[-1]] < n:
            q.pop()  # Remove indices whose values are less than current
        q.append(i)
        if q[0] == i - k:
            q.popleft()  # Remove indices out of the window
        if i >= k - 1:
            res.append(nums[q[0]])  # The front is the max in window
    return res
# Time complexity: O(n), Space complexity: O(k)
```

## 🔁 Variants

- Circular queue
- Monotonic queue (for range min/max)
- Double-ended queue (deque for palindrome checks)

---

