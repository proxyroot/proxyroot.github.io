---
title: "Binary Search Tree (BST) Overview"
date: 2024-06-01
categories: [data-structures]
tags: [bst, binary-search-tree, avl-tree, red-black-tree, treap, balanced-bst, data-structures, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, in-order-traversal, range-query, lca, problem-solving, big-o, time-complexity, self-balancing-tree, tree-map]
---

A Binary Search Tree (BST) is a binary tree that maintains sorted order for efficient search, insertion, and deletion. This post introduces BSTs, their properties, use cases, and provides practical Python examples and problems for understanding tree-based data structures.

## Overview

A **Binary Search Tree (BST)** is a binary tree where:

- All nodes in the left subtree of a node have smaller values
- All nodes in the right subtree have larger values

This structure allows **efficient search, insert, and delete** in `O(log n)` average time (but `O(n)` in worst-case).

---

## 🧩 Visualizing Binary Search Trees

### Sample BST

```mermaid
graph TD
    subgraph "Binary Search Tree"
        Root[5]
        L1[3]
        R1[7]
        LL1[1]
        LR1[4]
        RL1[6]
        RR1[8]
        
        Root --> L1
        Root --> R1
        L1 --> LL1
        L1 --> LR1
        R1 --> RL1
        R1 --> RR1
    end
    
    subgraph "BST Properties"
        P1[Left subtree of 5:<br/>[1, 3, 4] all < 5]
        P2[Right subtree of 5:<br/>[6, 7, 8] all > 5]
        P3[Left subtree of 3:<br/>[1] all < 3]
        P4[Right subtree of 3:<br/>[4] all > 3]
    end
    
    style Root fill:#ff9999
    style L1 fill:#99ccff
    style R1 fill:#99ff99
```

### BST Operations

```mermaid
graph TD
    subgraph "Search Operations"
        S1[Search 4:<br/>5 → 3 → 4 ✓]
        S2[Search 9:<br/>5 → 7 → 8 → null ✗]
    end
    
    subgraph "Modification Operations"
        I1[Insert 2:<br/>5 → 3 → 1 → right child]
        D1[Delete 3:<br/>Replace with inorder successor (4)]
    end
    
    subgraph "Search Path Visualization"
        Path1[5] --> Path2[3]
        Path2 --> Path3[4]
        Path3 --> Found[Found!]
        
        Path4[5] --> Path5[7]
        Path5 --> Path6[8]
        Path6 --> NotFound[Not Found]
    end
    
    style Found fill:#99ff99
    style NotFound fill:#ff9999
```

---

## 🛠️ How to Use (Python)

```python
# Definition of a binary search tree node and insert function
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val      # Value of the node
        self.left = left    # Left child
        self.right = right  # Right child

# Insert a value into the BST
def insert(root, val):
    if not root:
        return TreeNode(val)  # Create new node if tree is empty
    if val < root.val:
        root.left = insert(root.left, val)   # Insert in left subtree
    else:
        root.right = insert(root.right, val) # Insert in right subtree
    return root
# Time complexity: O(h), h = height of tree (O(log n) if balanced, O(n) worst case)

# Example:
root = None
for val in [5, 3, 7, 1, 4, 6, 8]:
    root = insert(root, val)
# Creates the BST shown above
```

---

## 🧩 Validate BST Step-by-Step

```mermaid
graph TD
    Start[Start: Validate BST]
    N5[Node 5: -∞ < 5 < +∞ ✓]
    N3[Node 3: -∞ < 3 < 5 ✓]
    N1[Node 1: -∞ < 1 < 3 ✓]
    N4[Node 4: 3 < 4 < 5 ✓]
    N7[Node 7: 5 < 7 < +∞ ✓]
    N6[Node 6: 5 < 6 < 7 ✓]
    N8[Node 8: 7 < 8 < +∞ ✓]
    Result[Result: True (all nodes valid)]

    Start --> N5 --> N3 --> N1
    N5 --> N7 --> N6
    N3 --> N4
    N7 --> N8
    N1 --> Result
    N4 --> Result
    N6 --> Result
    N8 --> Result
    style Result fill:#99ff99
```

Suppose we have this tree:
```
       5
     /   \
    3     7
   / \   / \
  1   4 6   8
```

| node | low | high | root.val | low < root.val < high? | action |
|------|-----|------|----------|------------------------|--------|
| 5    | -∞  | +∞   | 5        | -∞ < 5 < +∞ ✓         | Check children |
| 3    | -∞  | 5    | 3        | -∞ < 3 < 5 ✓          | Check children |
| 1    | -∞  | 3    | 1        | -∞ < 1 < 3 ✓          | Check children |
| 4    | 3   | 5    | 4        | 3 < 4 < 5 ✓           | Check children |
| 7    | 5   | +∞   | 7        | 5 < 7 < +∞ ✓          | Check children |
| 6    | 5   | 7    | 6        | 5 < 6 < 7 ✓           | Check children |
| 8    | 7   | +∞   | 8        | 7 < 8 < +∞ ✓          | Check children |

- All nodes satisfy BST property, return True.

---

## 📘 Sample Problem 1: Validate BST

> Check if a binary tree is a valid BST.

```python
def is_valid_bst(root, low=float('-inf'), high=float('inf')):
    if not root:
        return True
    if not (low < root.val < high):
        return False
    return is_valid_bst(root.left, low, root.val) and \
           is_valid_bst(root.right, root.val, high)

# Example:
# Create the BST from above
root = TreeNode(5)
root.left = TreeNode(3)
root.right = TreeNode(7)
root.left.left = TreeNode(1)
root.left.right = TreeNode(4)
root.right.left = TreeNode(6)
root.right.right = TreeNode(8)

print(is_valid_bst(root))  # Output: True
```

---

## 🧩 Lowest Common Ancestor Flow

```mermaid
graph TD
    Start[Start: Find LCA of nodes 1 and 4]
    N5[Node 5: 5 > 1 and 5 > 4 ✓ → Go left]
    N3[Node 3: 3 > 1 and 3 > 4 ✗ → Check other condition]
    N3Check[Node 3: 3 < 1 and 3 < 4 ✗ → Return 3]
    Result[Result: LCA = 3]

    Start --> N5 --> N3 --> N3Check --> Result
    style Result fill:#99ff99
```

Suppose we have the same BST and want to find LCA of nodes 1 and 4:

| node | p.val | q.val | root.val | root.val > p.val and root.val > q.val? | action |
|------|-------|-------|----------|----------------------------------------|--------|
| 5    | 1     | 4     | 5        | 5 > 1 and 5 > 4 ✓                     | Go left |
| 3    | 1     | 4     | 3        | 3 > 1 and 3 > 4 ✗                     | Check other condition |
| 3    | 1     | 4     | 3        | 3 < 1 and 3 < 4 ✗                     | Return 3 |

- LCA of 1 and 4 is 3.

---

## 📘 Sample Problem 2: Lowest Common Ancestor (LCA)

> Find the lowest common ancestor of two nodes in a BST.

```python
def lowest_common_ancestor(root, p, q):
    if root.val > p.val and root.val > q.val:
        return lowest_common_ancestor(root.left, p, q)
    if root.val < p.val and root.val < q.val:
        return lowest_common_ancestor(root.right, p, q)
    return root

# Example:
# Using the same BST from above
p = TreeNode(1)  # Node with value 1
q = TreeNode(4)  # Node with value 4
lca = lowest_common_ancestor(root, p, q)
print(lca.val)  # Output: 3
```

---

## 🔁 Variants

- Balanced BSTs (AVL, Red-Black Tree)
- Treap (BST + heap)
- Self-balancing BSTs (like in Java’s `TreeMap`)

---

