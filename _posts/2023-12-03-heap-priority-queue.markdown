---
title: "Heap / Priority Queue Overview"
date: 2023-12-03
categories: [data-structures]
tags: [heap, heaps, priority-queue, priority-queues, data-structures, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, scheduling, top-k, problem-solving, big-o, time-complexity, binary-tree]
---

A heap is a specialized binary tree used to efficiently maintain a dynamic ordering of elements, and a priority queue is an abstract data type built on heaps. This post explains heaps and priority queues, their use cases, and provides practical Python examples and problems.

## Overview

A **heap** is a complete binary tree used to maintain a dynamic ordering of elements:

- **Min-heap**: parent ≤ children → smallest on top
- **Max-heap**: parent ≥ children → largest on top

A **priority queue** is an abstract data type where each element has a priority. A heap is a common implementation.

In Python, use `heapq` (which is a min-heap by default).

## 🧩 Visualizing Heaps

### Min-Heap Structure

```
Array: [1, 3, 4, 5, 6, 7]

Tree representation:
       1
     /   \
    3     4
   / \   /
  5   6 7

Index mapping:
Parent: (i-1)//2
Left child: 2*i + 1
Right child: 2*i + 2
```

### Heap Operations

```
Insert 2: [1, 3, 4, 5, 6, 7, 2] → [1, 3, 2, 5, 6, 7, 4]
Extract min: [1, 3, 2, 5, 6, 7, 4] → [2, 3, 4, 5, 6, 7]
```

---

## 🛠️ How to Use (Python)

```python
# Basic heap (priority queue) operations in Python
# - Use heapq for min-heap, use negative values for max-heap
import heapq

heap = []
heapq.heappush(heap, 3)    # Push 3 onto the heap
heapq.heappush(heap, 1)    # Push 1 onto the heap
heapq.heappush(heap, 4)    # Push 4 onto the heap
heapq.heappop(heap)        # Pop and return the smallest item (returns 1)

# Max-heap: insert negative values
heapq.heappush(heap, -5)   # Push -5 (acts as 5 in max-heap)
-heapq.heappop(heap)       # Pop and return the largest item (returns 5)
# All heap operations above are O(log n)

# Example:
heap = []
heapq.heappush(heap, 10)
heapq.heappush(heap, 5)
heapq.heappush(heap, 15)
print(heapq.heappop(heap))  # Output: 5
print(heap)                 # Output: [10, 15]
```

---

## 🧩 Kth Largest Element Step-by-Step

Suppose nums = [3, 2, 1, 5, 6, 4], k = 2

| Step | num | heap before | num > heap[0]? | heap after | Action |
|------|----|-------------|----------------|------------|--------|
| 1    | -  | [3, 2, 1]   | -              | [3, 2, 1]  | Initial |
| 2    | 5  | [3, 2, 1]   | 5 > 1          | [3, 2, 5]  | Replace 1 |
| 3    | 6  | [3, 2, 5]   | 6 > 2          | [3, 5, 6]  | Replace 2 |
| 4    | 4  | [3, 5, 6]   | 4 > 3          | [4, 5, 6]  | Replace 3 |

- Final result: 5 (2nd largest element).

---

## 📘 Sample Problem 1: Kth Largest Element

> Find the kth largest element in an array.

```python
import heapq

# This function finds the kth largest element using a min-heap of size k.
def find_kth_largest(nums, k):
    heap = nums[:k]              # Take first k elements
    heapq.heapify(heap)          # Heapify them (min-heap)
    for num in nums[k:]:
        if num > heap[0]:
            heapq.heappushpop(heap, num)  # Replace smallest if current is bigger
    return heap[0]               # The root is the kth largest
# Time complexity: O(n log k), Space complexity: O(k)

# Example:
nums = [3, 2, 1, 5, 6, 4]
k = 2
print(find_kth_largest(nums, k))  # Output: 5
```

---

## 🧩 Merge K Sorted Lists Flow

Suppose lists = [[1,4,5], [1,3,4], [2,6]]

| Step | heap | min_val | list_idx | node | result |
|------|------|---------|----------|------|--------|
| 0    | [(1,0,1), (1,1,1), (2,2,2)] | - | - | - | [] |
| 1    | [(1,1,1), (2,2,2), (4,0,4)] | 1 | 0 | 1 | [1] |
| 2    | [(1,1,1), (2,2,2), (4,0,4)] | 1 | 1 | 1 | [1,1] |
| 3    | [(2,2,2), (3,1,3), (4,0,4)] | 2 | 2 | 2 | [1,1,2] |
| 4    | [(3,1,3), (4,0,4), (4,1,4)] | 3 | 1 | 3 | [1,1,2,3] |
| 5    | [(4,0,4), (4,1,4), (6,2,6)] | 4 | 0 | 4 | [1,1,2,3,4] |
| 6    | [(4,1,4), (5,0,5), (6,2,6)] | 4 | 1 | 4 | [1,1,2,3,4,4] |
| 7    | [(5,0,5), (6,2,6)] | 5 | 0 | 5 | [1,1,2,3,4,4,5] |
| 8    | [(6,2,6)] | 6 | 2 | 6 | [1,1,2,3,4,4,5,6] |

- Final result: [1,1,2,3,4,4,5,6]

---

## 📘 Sample Problem 2: Merge K Sorted Lists

> Merge k sorted linked lists into one sorted list.

```python
import heapq

# This function merges k sorted linked lists using a heap.
def merge_k_lists(lists):
    heap = []
    for i, node in enumerate(lists):
        if node:
            heapq.heappush(heap, (node.val, i, node))  # Push (value, list index, node)

    dummy = curr = ListNode(0)
    while heap:
        val, i, node = heapq.heappop(heap)
        curr.next = node
        curr = curr.next
        if node.next:
            heapq.heappush(heap, (node.next.val, i, node.next))
    return dummy.next
# Time complexity: O(N log k), where N = total nodes, k = number of lists
# Space complexity: O(k)

# Example:
# lists = [[1,4,5], [1,3,4], [2,6]]
# result = [1,1,2,3,4,4,5,6]
```

---

## 🔁 Variants

- Max-heap (negate values)
- Custom comparator with `(priority, index, value)`
- Double-ended priority queues (not natively supported in Python)

---

