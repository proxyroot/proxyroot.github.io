---
title: "Heap / Priority Queue Overview"
date: 2023-12-03
categories: [data-structures]
tags: [heap, heaps, priority-queue, priority-queues, data-structures, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, scheduling, top-k, problem-solving, big-o, time-complexity, binary-tree]
---

A heap is a specialized binary tree used to efficiently maintain a dynamic ordering of elements, and a priority queue is an abstract data type built on heaps. This post explains heaps and priority queues, their use cases, and provides practical Python examples and problems.

## ✅ What It Is

A **heap** is a complete binary tree used to maintain a dynamic ordering of elements:

- **Min-heap**: parent ≤ children → smallest on top
- **Max-heap**: parent ≥ children → largest on top

A **priority queue** is an abstract data type where each element has a priority. A heap is a common implementation.

In Python, use `heapq` (which is a min-heap by default).

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
```

## 📦 Use Cases

- Priority scheduling (OS, Dijkstra’s)
- Top-K problems (largest/smallest)
- Streaming median
- Huffman coding

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
```

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
```

## 🔁 Variants

- Max-heap (negate values)
- Custom comparator with `(priority, index, value)`
- Double-ended priority queues (not natively supported in Python)

---

