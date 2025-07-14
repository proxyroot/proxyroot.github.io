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
    Root[5] --> L1[3]
    Root --> R1[7]
    L1 --> LL1[1]
    L1 --> LR1[4]
    R1 --> RL1[6]
    R1 --> RR1[8]
    
    style Root fill:#ff9999
    style L1 fill:#99ccff
    style R1 fill:#99ff99
```

### BST Operations

```mermaid
graph TD
    S1[Search 4: 5 to 3 to 4 Found]
    S2[Search 9: 5 to 7 to 8 Not Found]
    I1[Insert 2: 5 to 3 to 1 right child]
    D1[Delete 3: Replace with inorder successor 4]
    
    Path1[5] --> Path2[3]
    Path2 --> Path3[4]
    Path3 --> Found[Found]
    
    Path4[5] --> Path5[7]
    Path5 --> Path6[8]
    Path6 --> NotFound[Not Found]
    
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
    Start[Start Validate BST]
    N5[Node 5: -inf to 5 to inf Valid]
    N3[Node 3: -inf to 3 to 5 Valid]
    N1[Node 1: -inf to 1 to 3 Valid]
    N4[Node 4: 3 to 4 to 5 Valid]
    N7[Node 7: 5 to 7 to inf Valid]
    N6[Node 6: 5 to 6 to 7 Valid]
    N8[Node 8: 7 to 8 to inf Valid]
    Result[Result: True all nodes valid]

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

## 🧩 What is Lowest Common Ancestor (LCA)?

**Lowest Common Ancestor (LCA)** is the deepest node that has both target nodes as descendants (or the node itself if it's one of the targets).

### Visual Example

```mermaid
graph TD
    Root[5] --> L1[3]
    Root --> R1[7]
    L1 --> LL1[1]
    L1 --> LR1[4]
    R1 --> RL1[6]
    R1 --> RR1[8]
    
    style Root fill:#ff9999
    style L1 fill:#99ff99
    style LL1 fill:#99ccff
    style LR1 fill:#99ccff
    style R1 fill:#ffcc99
    style RL1 fill:#ffcc99
    style RR1 fill:#ffcc99
```

**Examples:**
- **LCA of 1 and 4**: Node **3** (both 1 and 4 are descendants of 3)
- **LCA of 1 and 6**: Node **5** (1 is in left subtree, 6 is in right subtree)
- **LCA of 6 and 8**: Node **7** (both 6 and 8 are descendants of 7)
- **LCA of 3 and 4**: Node **3** (3 is ancestor of 4)

### Why LCA is Useful?

1. **Path Finding**: LCA helps find shortest path between two nodes
2. **Tree Analysis**: Understanding relationships between nodes
3. **Range Queries**: Finding common ancestors for range operations

---

## 🧩 LCA Algorithm Flow

### The Key Insight: BST Property

In a BST, if both target values are **smaller** than current node → go **left**
If both target values are **larger** than current node → go **right**
Otherwise → current node is the LCA!

```mermaid
graph TD
    Start[Start: Find LCA of nodes 1 and 4]
    Check5[Check Node 5: 1 < 5 and 4 < 5?]
    GoLeft[Yes! Go to left child Node 3]
    Check3[Check Node 3: 1 < 3 and 4 > 3?]
    Found[No! Node 3 is the LCA]
    Result[Result: LCA is Node 3]

    Start --> Check5 --> GoLeft --> Check3 --> Found --> Result
    style Found fill:#99ff99
    style Result fill:#99ff99
```

### Step-by-Step Process

| Step | Current Node | Check | Decision | Reason |
|------|-------------|-------|----------|---------|
| 1 | 5 | 1 < 5 and 4 < 5? | Go Left | Both targets are smaller |
| 2 | 3 | 1 < 3 and 4 > 3? | **Found LCA** | Targets are on different sides |

**Why Node 3 is the LCA:**
- Node 1 is in the **left subtree** of 3
- Node 4 is in the **right subtree** of 3
- This means 3 is the **lowest** node that contains both 1 and 4

---

## 📘 Sample Problem 2: Lowest Common Ancestor (LCA)

> Find the lowest common ancestor of two nodes in a BST.

```python
def lowest_common_ancestor(root, p, q):
    """
    Find the lowest common ancestor of nodes p and q in a BST.
    
    The key insight: Use BST property to navigate efficiently.
    - If both p and q are smaller than root → go left
    - If both p and q are larger than root → go right  
    - Otherwise → root is the LCA
    """
    # If both values are smaller, go to left subtree
    if root.val > p.val and root.val > q.val:
        return lowest_common_ancestor(root.left, p, q)
    
    # If both values are larger, go to right subtree
    if root.val < p.val and root.val < q.val:
        return lowest_common_ancestor(root.right, p, q)
    
    # Otherwise, root is the LCA
    # (one value is smaller, one is larger, or root equals one of them)
    return root

# Example usage:
# Create the BST:       5
#                     /   \
#                    3     7
#                   / \   / \
#                  1   4 6   8

root = TreeNode(5)
root.left = TreeNode(3)
root.right = TreeNode(7)
root.left.left = TreeNode(1)
root.left.right = TreeNode(4)
root.right.left = TreeNode(6)
root.right.right = TreeNode(8)

# Test cases
p = TreeNode(1)  # Node with value 1
q = TreeNode(4)  # Node with value 4
lca = lowest_common_ancestor(root, p, q)
print(f"LCA of {p.val} and {q.val} is: {lca.val}")  # Output: 3

# More examples
p = TreeNode(1)
q = TreeNode(6)
lca = lowest_common_ancestor(root, p, q)
print(f"LCA of {p.val} and {q.val} is: {lca.val}")  # Output: 5

p = TreeNode(6)
q = TreeNode(8)
lca = lowest_common_ancestor(root, p, q)
print(f"LCA of {q.val} and {q.val} is: {lca.val}")  # Output: 7
```

---

## 🔁 Variants

- Balanced BSTs (AVL, Red-Black Tree)
- Treap (BST + heap)
- Self-balancing BSTs (like in Java’s `TreeMap`)

---

