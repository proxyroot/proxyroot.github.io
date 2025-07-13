---
title: "Linked List Overview"
date: 2024-03-03
categories: [data-structures]
tags: [linked-list, python]
---

A linked list is a linear data structure where each element points to the next, allowing efficient insertions and deletions. This post explains the fundamentals of linked lists, their types, use cases, and provides practical Python examples and problems.

# Linked List

## ✅ What It Is

A **linked list** is a linear data structure where elements (nodes) point to the next node.

- **Singly Linked List**: node → next
- **Doubly Linked List**: node ↔ next & prev

Each node contains:

- A **value**
- A **pointer** to the next (and/or previous) node

## 🛠️ How to Use (Python)

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

# Create list: 1 → 2 → 3
head = ListNode(1, ListNode(2, ListNode(3)))
```

## 📦 Use Cases

- Dynamic memory allocation
- Implementing stacks/queues
- LRU caches
- Reversal, merging, cycle detection problems

## 📘 Sample Problem 1: Reverse Linked List

> Reverse a singly linked list.

```python
def reverse_list(head):
    prev = None
    curr = head
    while curr:
        next_node = curr.next
        curr.next = prev
        prev = curr
        curr = next_node
    return prev
```

## 📘 Sample Problem 2: Detect Cycle

> Return True if the linked list has a cycle.

```python
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True
    return False
```

## 🔁 Variants

- Doubly linked list
- Circular linked list
- Skip list (layered linked list)

---

