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

---

## 🧩 Visualizing Linked Lists

### Singly Linked List

```mermaid
graph LR
    N1[1] --> N2[2]
    N2 --> N3[3]
    N3 --> N4[4]
    N4 --> Null[null]
    style N1 fill:#ff9999
    style N2 fill:#99ccff
    style N3 fill:#99ff99
    style N4 fill:#ffcc99
    style Null fill:#eeeeee
```

### Doubly Linked List

```mermaid
graph LR
    Null1[null] <--> D1[1]
    D1 <--> D2[2]
    D2 <--> D3[3]
    D3 <--> D4[4]
    D4 --> Null2[null]
    style D1 fill:#ff9999
    style D2 fill:#99ccff
    style D3 fill:#99ff99
    style D4 fill:#ffcc99
    style Null1 fill:#eeeeee
    style Null2 fill:#eeeeee
```

---

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

# Example:
def print_list(head):
    curr = head
    while curr:
        print(curr.val, end=" → ")
        curr = curr.next
    print("null")

print_list(head)  # Output: 1 → 2 → 3 → null
```

---

## 🧩 Reverse Linked List Step-by-Step

```mermaid
graph TD
    Start[Start: 1 → 2 → 3 → null]
    Step1[Step 1: prev=null, curr=1, next=2<br/>1 → null, prev=1, curr=2]
    Step2[Step 2: prev=1, curr=2, next=3<br/>2 → 1 → null, prev=2, curr=3]
    Step3[Step 3: prev=2, curr=3, next=null<br/>3 → 2 → 1 → null, prev=3, curr=null]
    Result[Result: 3 → 2 → 1 → null]

    Start --> Step1 --> Step2 --> Step3 --> Result
    style Result fill:#99ff99
```

Suppose head = 1 → 2 → 3 → null

| Step | prev | curr | next_node | curr.next | Action |
|------|------|------|-----------|-----------|--------|
| 0    | null | 1    | 2         | null      | 1 → null |
| 1    | 1    | 2    | 3         | 1         | 2 → 1 → null |
| 2    | 2    | 3    | null      | 2         | 3 → 2 → 1 → null |
| 3    | 3    | null | -         | -         | Return 3 |

- Final result: 3 → 2 → 1 → null

---

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

# Example:
head = ListNode(1, ListNode(2, ListNode(3)))
reversed_head = reverse_list(head)
print_list(reversed_head)  # Output: 3 → 2 → 1 → null
```

---

## 🧩 Cycle Detection Flow

```mermaid
graph TD
    Start[Start: 1 to 2 to 3 to 4 to 2 cycle]
    Step1[Step 1: slow=1 fast=1 to slow=2 fast=3]
    Step2[Step 2: slow=2 fast=3 to slow=3 fast=2]
    Step3[Step 3: slow=3 fast=2 to slow=4 fast=4]
    Cycle[Cycle detected: slow=4 fast=4]
    Result[Result: True cycle found]

    Start --> Step1 --> Step2 --> Step3 --> Cycle --> Result
    style Result fill:#99ff99
```

Suppose list: 1 → 2 → 3 → 4 → 2 (cycle back to node 2)

| Step | slow | fast | slow.next | fast.next.next | slow == fast? |
|------|------|------|-----------|----------------|---------------|
| 0    | 1    | 1    | 2         | 3              | 1 == 1? No   |
| 1    | 2    | 3    | 3         | 2              | 2 == 3? No   |
| 2    | 3    | 2    | 4         | 4              | 3 == 2? No   |
| 3    | 4    | 4    | 2         | 3              | 4 == 4? Yes  |

- Cycle detected at step 3.

---

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

# Example:
# Create a cycle: 1 → 2 → 3 → 4 → 2
head = ListNode(1)
head.next = ListNode(2)
head.next.next = ListNode(3)
head.next.next.next = ListNode(4)
head.next.next.next.next = head.next  # Create cycle

print(has_cycle(head))  # Output: True
```

---

## 🔁 Variants

- Doubly linked list
- Circular linked list
- Skip list (layered linked list)

---

