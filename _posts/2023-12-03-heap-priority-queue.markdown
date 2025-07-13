---
title: "Heap / Priority Queue Overview"
date: 2023-12-03
categories: [data-structures]
tags: [heap, priority-queue, python]
---

# Heap / Priority Queue

## ✅ What It Is

A **heap** is a complete binary tree used to maintain a dynamic ordering of elements:

- **Min-heap**: parent ≤ children → smallest on top
- **Max-heap**: parent ≥ children → largest on top

A **priority queue** is an abstract data type where each element has a priority. A heap is a common implementation.

In Python, use `heapq` (which is a min-heap by default).

## 🛠️ How to Use (Python)

```python
import heapq

heap = []
heapq.heappush(heap, 3)
heapq.heappush(heap, 1)
heapq.heappush(heap, 4)
heapq.heappop(heap)     # returns 1 (smallest)

# Max-heap: insert negative values
heapq.heappush(heap, -val)
heapq.heappop(heap) * -1
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

def find_kth_largest(nums, k):
    heap = nums[:k]
    heapq.heapify(heap)
    for num in nums[k:]:
        if num > heap[0]:
            heapq.heappushpop(heap, num)
    return heap[0]
```

## 📘 Sample Problem 2: Merge K Sorted Lists

> Merge k sorted linked lists into one sorted list.

```python
import heapq

def merge_k_lists(lists):
    heap = []
    for i, node in enumerate(lists):
        if node:
            heapq.heappush(heap, (node.val, i, node))

    dummy = curr = ListNode(0)
    while heap:
        val, i, node = heapq.heappop(heap)
        curr.next = node
        curr = curr.next
        if node.next:
            heapq.heappush(heap, (node.next.val, i, node.next))
    return dummy.next
```

## 🔁 Variants

- Max-heap (negate values)
- Custom comparator with `(priority, index, value)`
- Double-ended priority queues (not natively supported in Python)

---

