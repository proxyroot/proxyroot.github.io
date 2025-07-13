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

```
Values: [5, 3, 7, 1, 4, 6, 8]

Tree representation:
       5
     /   \
    3     7
   / \   / \
  1   4 6   8

BST Properties:
- Left subtree of 5: [1, 3, 4] (all < 5)
- Right subtree of 5: [6, 7, 8] (all > 5)
- Left subtree of 3: [1] (all < 3)
- Right subtree of 3: [4] (all > 3)
```

### BST Operations

```
Search 4: 5 → 3 → 4 (found)
Search 9: 5 → 7 → 8 → null (not found)
Insert 2: 5 → 3 → 1 → right child of 1
Delete 3: Replace with inorder successor (4)
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

