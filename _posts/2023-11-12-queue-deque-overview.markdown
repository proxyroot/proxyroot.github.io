---
title: "Queue / Deque Overview"
date: 2023-11-12
categories: [data-structures]
tags: [queue, deque, python]
---

A queue is a First-In-First-Out (FIFO) data structure, while a deque allows adding and removing elements from both ends efficiently. This post explores queues and deques in Python, their operations, use cases, and provides practical examples and problems.

## ✅ What It Is

A **queue** is a First-In-First-Out (FIFO) data structure. A **deque** (double-ended queue) allows adding/removing from both ends efficiently.

In Python:

- Use `collections.deque` for both queue and deque behavior
- Use `queue.Queue` for thread-safe queues (multithreading)

## 🛠️ How to Use (Python)

```python
from collections import deque

q = deque()
q.append(1)     # enqueue
q.popleft()     # dequeue

q.appendleft(2) # add to front
q.pop()         # remove from end
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

class MovingAverage:
    def __init__(self, size):
        self.q = deque()
        self.k = size
        self.sum = 0

    def next(self, val):
        self.q.append(val)
        self.sum += val
        if len(self.q) > self.k:
            self.sum -= self.q.popleft()
        return self.sum / len(self.q)
```

## 📘 Sample Problem 2: Sliding Window Maximum

> Given an array `nums` and window size `k`, return max of each window.

```python
from collections import deque

def max_sliding_window(nums, k):
    q = deque()
    res = []
    for i, n in enumerate(nums):
        while q and nums[q[-1]] < n:
            q.pop()
        q.append(i)
        if q[0] == i - k:
            q.popleft()
        if i >= k - 1:
            res.append(nums[q[0]])
    return res
```

## 🔁 Variants

- Circular queue
- Monotonic queue (for range min/max)
- Double-ended queue (deque for palindrome checks)

---

