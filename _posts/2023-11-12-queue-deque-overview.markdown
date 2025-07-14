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

---

## 🧩 Visualizing Queues and Deques

### Queue Operations (FIFO)

```mermaid
graph LR
    Initial[Initial: empty]
    E1[Enqueue 1]
    E2[Enqueue 2]
    E3[Enqueue 3]
    D1[Dequeue returns 1]
    D2[Dequeue returns 2]
    P1[Peek returns 3]
    
    Initial --> E1 --> E2 --> E3 --> D1 --> D2 --> P1
    
    Q1[Queue: 1]
    Q2[Queue: 1,2]
    Q3[Queue: 1,2,3]
    Q4[Queue: 2,3]
    Q5[Queue: 3]
    Q6[Queue: 3]
    
    E1 -.-> Q1
    E2 -.-> Q2
    E3 -.-> Q3
    D1 -.-> Q4
    D2 -.-> Q5
    P1 -.-> Q6
    
    style Initial fill:#f9f9f9
    style P1 fill:#99ff99
```

### Deque Operations (Double-ended)

```mermaid
graph LR
    Start[Initial: empty]
    A1[Append 1]
    AL2[Appendleft 2]
    A3[Append 3]
    P1[Pop returns 3]
    PL1[Popleft returns 2]
    
    Start --> A1 --> AL2 --> A3 --> P1 --> PL1
    
    D1[Deque: 1]
    D2[Deque: 2,1]
    D3[Deque: 2,1,3]
    D4[Deque: 2,1]
    D5[Deque: 1]
    
    A1 -.-> D1
    AL2 -.-> D2
    A3 -.-> D3
    P1 -.-> D4
    PL1 -.-> D5
    
    style Start fill:#f9f9f9
    style PL1 fill:#99ff99
```

---

## 🛠️ How to Use (Python)

```python
# Basic queue and deque operations in Python
- Use collections.deque for efficient queue and deque behavior
from collections import deque

q = deque()
q.append(1)         # Enqueue 1 at the end
q.popleft()         # Dequeue from the front
q.appendleft(2)     # Add 2 to the front
q.pop()             # Remove from the end
# All operations above are O(1)

# Example:
q = deque()
q.append(10)
q.append(20)
q.append(30)
print(q.popleft())  # Output: 10
print(q)            # Output: deque([20, 30])
```

---

## 🧩 Moving Average Flow

```mermaid
graph TD
    Start[Start: window_size = 3]
    Step1[Step 1: val=1 to q=1 avg=1.0]
    Step2[Step 2: val=10 to q=1,10 avg=5.5]
    Step3[Step 3: val=3 to q=1,10,3 avg=4.67]
    Step4[Step 4: val=5 to q=10,3,5 avg=6.0]
    Result[Result: Moving averages calculated]

    Start --> Step1 --> Step2 --> Step3 --> Step4 --> Result
    style Result fill:#99ff99
```

## 🧩 Moving Average Step-by-Step

Suppose size = 3, values = [1, 10, 3, 5]

| Step | val | q before | q after | sum | len(q) | avg |
|------|----|----------|---------|-----|--------|-----|
| 1    | 1  | []       | [1]     | 1   | 1      | 1.0 |
| 2    | 10 | [1]      | [1,10]  | 11  | 2      | 5.5 |
| 3    | 3  | [1,10]   | [1,10,3]| 14  | 3      | 4.67|
| 4    | 5  | [1,10,3] | [10,3,5]| 18  | 3      | 6.0 |

- When q exceeds size, remove oldest element.

---

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

# Example:
ma = MovingAverage(3)
print(ma.next(1))   # Output: 1.0
print(ma.next(10))  # Output: 5.5
print(ma.next(3))   # Output: 4.67
print(ma.next(5))   # Output: 6.0
```

---

## 🧩 Sliding Window Maximum Flow

```mermaid
graph TD
    Start[Start: nums=1,3,-1,-3,5,3,6,7 k=3]
    Step1[Step 1: i=0 q=0 res=empty]
    Step2[Step 2: i=1 q=1 res=empty]
    Step3[Step 3: i=2 q=1,2 res=3]
    Step4[Step 4: i=3 q=1,2,3 res=3,3]
    Step5[Step 5: i=4 q=4 res=3,3,5]
    Step6[Step 6: i=5 q=4,5 res=3,3,5,5]
    Step7[Step 7: i=6 q=6 res=3,3,5,5,6]
    Step8[Step 8: i=7 q=7 res=3,3,5,5,6,7]
    Result[Result: 3,3,5,5,6,7]

    Start --> Step1 --> Step2 --> Step3 --> Step4 --> Step5 --> Step6 --> Step7 --> Step8 --> Result
    style Result fill:#99ff99
```

Suppose nums = [1, 3, -1, -3, 5, 3, 6, 7], k = 3

| i | nums[i] | q before | nums[q[-1]] < nums[i]? | q after | q[0] == i-k? | res |
|---|---------|----------|------------------------|---------|--------------|-----|
| 0 | 1       | []       | -                      | [0]     | 0 == -3?     | []  |
| 1 | 3       | [0]      | 1 < 3                  | [1]     | 1 == -2?     | []  |
| 2 | -1      | [1]      | 3 < -1                 | [1,2]   | 1 == -1?     | [3] |
| 3 | -3      | [1,2]    | -1 < -3                | [1,2,3] | 1 == 0?      | [3] |
| 4 | 5       | [1,2,3]  | -3 < 5, -1 < 5, 3 < 5 | [4]     | 4 == 1?      | [3,3]|
| 5 | 3       | [4]      | 5 < 3                  | [4,5]   | 4 == 2?      | [3,3,5]|
| 6 | 6       | [4,5]    | 3 < 6, 5 < 6           | [6]     | 6 == 3?      | [3,3,5,5]|
| 7 | 7       | [6]      | 6 < 7                  | [7]     | 7 == 4?      | [3,3,5,5,6]|

- Result: [3, 3, 5, 5, 6, 7]

---

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

# Example:
nums = [1, 3, -1, -3, 5, 3, 6, 7]
k = 3
print(max_sliding_window(nums, k))  # Output: [3, 3, 5, 5, 6, 7]
```

---

## 🔁 Variants

- Circular queue
- Monotonic queue (for range min/max)
- Double-ended queue (deque for palindrome checks)

---

