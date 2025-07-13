---
title: "Linked List Overview"
date: 2024-03-03
categories: [data-structures]
tags: [linked-list, linked-lists, singly-linked-list, doubly-linked-list, circular-linked-list, skip-list, data-structures, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, pointers, reversal, cycle-detection, problem-solving, big-o, time-complexity, memory-management]
---

A linked list is a linear data structure where each element points to the next, allowing efficient insertions and deletions. This post explains the fundamentals of linked lists, their types, use cases, and provides practical Python examples and problems.

# Linked List

## Overview

A **linked list** is a linear data structure where elements (nodes) point to the next node.

- **Singly Linked List**: node → next
- **Doubly Linked List**: node ↔ next & prev

Each node contains:

- A **value**
- A **pointer** to the next (and/or previous) node

## 🛠️ How to Use (Python)

```python
# Basic singly linked list node and creation in Python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val      # Value of the node
        self.next = next    # Pointer to the next node

# Create a linked list: 1 → 2 → 3
head = ListNode(1, ListNode(2, ListNode(3)))
# Traversal and most operations are O(n)
```

## 📦 Use Cases

- Dynamic memory allocation
- Implementing stacks/queues
- LRU caches
- Reversal, merging, cycle detection problems

## 📘 Sample Problem 1: Reverse Linked List

> Reverse a singly linked list.

```python
# This function reverses a singly linked list.
def reverse_list(head):
    prev = None
    curr = head
    while curr:
        next_node = curr.next      # Store next node
        curr.next = prev          # Reverse the link
        prev = curr               # Move prev forward
        curr = next_node          # Move curr forward
    return prev                   # New head of reversed list
# Time complexity: O(n), Space complexity: O(1)
```

## 📘 Sample Problem 2: Detect Cycle

> Return True if the linked list has a cycle.

```python
# This function detects if a linked list has a cycle using two pointers.
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next         # Move slow by 1
        fast = fast.next.next    # Move fast by 2
        if slow == fast:
            return True          # Cycle detected
    return False                 # No cycle
# Time complexity: O(n), Space complexity: O(1)
```

## 🔁 Variants

- Doubly linked list
- Circular linked list
- Skip list (layered linked list)

---

