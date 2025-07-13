---
title: "Union-Find / Disjoint Set Overview"
date: 2024-04-07
categories: [data-structures]
tags: [union-find, disjoint-set, union-find-set, data-structures, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, connected-components, kruskal, minimum-spanning-tree, cycle-detection, problem-solving, big-o, time-complexity, path-compression, union-by-rank, dynamic-connectivity]
---

Union-Find (Disjoint Set) is a data structure for efficiently managing and merging disjoint sets, supporting fast union and find operations. This post covers the principles of union-find, its applications, and provides practical Python examples and problems.

## Overview

A **disjoint set** (or **Union-Find**) is a data structure to keep track of a partition of elements into disjoint (non-overlapping) sets.

Supports two main operations efficiently:

- `find(x)`: returns the root/representative of the set that contains `x`
- `union(x, y)`: merges the sets containing `x` and `y`

With **path compression** and **union by rank**, both operations run in nearly constant time: `O(α(n))`.

## 🧩 Visualizing Union-Find

### Initial State

```mermaid
graph TD
    subgraph "Initial Union-Find Structure"
        N0[Node 0<br/>Parent: 0<br/>Rank: 0]
        N1[Node 1<br/>Parent: 1<br/>Rank: 0]
        N2[Node 2<br/>Parent: 2<br/>Rank: 0]
        N3[Node 3<br/>Parent: 3<br/>Rank: 0]
        N4[Node 4<br/>Parent: 4<br/>Rank: 0]
    end
    
    style N0 fill:#ff9999
    style N1 fill:#99ccff
    style N2 fill:#99ff99
    style N3 fill:#ffcc99
    style N4 fill:#ff99cc
```

### After Union Operations

```mermaid
graph TD
    subgraph "Union Operations"
        U1[union(0, 1)<br/>parent[0] = 1<br/>rank[1] = 1]
        U2[union(2, 3)<br/>parent[2] = 3<br/>rank[3] = 1]
        U3[union(1, 2)<br/>parent[3] = 1<br/>rank[1] = 2]
    end
    
    subgraph "Final Tree Structure"
        Root[Node 1<br/>Parent: 1<br/>Rank: 2]
        Left[Node 0<br/>Parent: 1<br/>Rank: 0]
        Right[Node 3<br/>Parent: 1<br/>Rank: 0]
        Child[Node 2<br/>Parent: 3<br/>Rank: 0]
        Isolated[Node 4<br/>Parent: 4<br/>Rank: 0]
        
        Root --> Left
        Root --> Right
        Right --> Child
    end
    
    style Root fill:#ff9999
    style Left fill:#99ccff
    style Right fill:#99ff99
    style Child fill:#ffcc99
    style Isolated fill:#ff99cc
```

---

## 🛠️ How to Use (Python)

```python
# Implementation of Union-Find (Disjoint Set) with path compression and union by rank
class UnionFind:
    def __init__(self, size):
        self.parent = list(range(size))  # Each node is its own parent initially
        self.rank = [0] * size           # Used to keep tree flat

    def find(self, x):
        # Find the root of x with path compression
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # Path compression
        return self.parent[x]

    def union(self, x, y):
        # Union by rank: attach smaller tree to larger
        rootX = self.find(x)
        rootY = self.find(y)
        if rootX == rootY:
            return False  # Already in the same set
        if self.rank[rootX] < self.rank[rootY]:
            self.parent[rootX] = rootY
        elif self.rank[rootX] > self.rank[rootY]:
            self.parent[rootY] = rootX
        else:
            self.parent[rootY] = rootX
            self.rank[rootX] += 1
        return True
# All operations above are nearly O(1) due to path compression and union by rank

# Example:
uf = UnionFind(5)
uf.union(0, 1)
uf.union(2, 3)
uf.union(1, 2)
print(uf.find(0))  # Output: 1
print(uf.find(3))  # Output: 1
print(uf.find(4))  # Output: 4
```

---

## 🧩 Union Operations Step-by-Step

```mermaid
graph TD
    Start[Start: parent = [0,1,2,3,4], rank = [0,0,0,0,0]]
    U1[union(0,1): parent[0]=1, rank[1]=1]
    U2[union(2,3): parent[2]=3, rank[3]=1]
    U3[union(1,2): parent[3]=1, rank[1]=2]
    Final[Final: parent=[1,1,3,1,4], rank=[0,2,0,0,0]]

    Start --> U1 --> U2 --> U3 --> Final
    style Final fill:#99ff99
```

Suppose n = 5, edges = [(0,1), (2,3), (1,2)]

| Step | edge | find(0) | find(1) | find(2) | find(3) | find(4) | parent | rank |
|------|------|---------|---------|---------|---------|---------|--------|------|
| 0    | -    | 0       | 1       | 2       | 3       | 4       | [0,1,2,3,4] | [0,0,0,0,0] |
| 1    | (0,1)| 1       | 1       | 2       | 3       | 4       | [1,1,2,3,4] | [0,1,0,0,0] |
| 2    | (2,3)| 1       | 1       | 3       | 3       | 4       | [1,1,3,3,4] | [0,1,0,1,0] |
| 3    | (1,2)| 1       | 1       | 1       | 1       | 4       | [1,1,3,1,4] | [0,2,0,0,0] |

- Final result: 2 connected components (set with root 1 and singleton set with root 4).

---

## 🧩 Connected Components Flow

```mermaid
graph TD
    S0[Step 0: Components = 5, Roots = {0,1,2,3,4}]
    E1[Union (0,1): Components = 4, Roots = {1,2,3,4}]
    E2[Union (1,2): Components = 3, Roots = {1,3,4}]
    E3[Union (3,4): Components = 2, Roots = {1,4}]
    Result[Result: 2 connected components]

    S0 --> E1 --> E2 --> E3 --> Result
    style Result fill:#99ff99
```

Suppose n = 5, edges = [(0,1), (1,2), (3,4)]

| Step | edge | uf.find(i) for i in range(5) | unique_roots | components |
|------|------|------------------------------|--------------|------------|
| 0    | -    | [0, 1, 2, 3, 4]            | {0,1,2,3,4} | 5          |
| 1    | (0,1)| [1, 1, 2, 3, 4]            | {1,2,3,4}   | 4          |
| 2    | (1,2)| [1, 1, 1, 3, 4]            | {1,3,4}     | 3          |
| 3    | (3,4)| [1, 1, 1, 4, 4]            | {1,4}       | 2          |

- Final result: 2 connected components.

---

## 📘 Sample Problem 1: Number of Connected Components

> Given `n` nodes and a list of undirected edges, return the number of connected components.

```python
def count_components(n, edges):
    uf = UnionFind(n)
    for u, v in edges:
        uf.union(u, v)
    return len(set(uf.find(i) for i in range(n)))

# Example:
n = 5
edges = [(0,1), (1,2), (3,4)]
print(count_components(n, edges))  # Output: 2
```

---

## 📘 Sample Problem 2: Accounts Merge

> Merge email accounts that belong to the same user (based on overlapping emails).

```python
def accounts_merge(accounts):
    uf = UnionFind(len(accounts))
    email_to_id = {}
    
    # Union accounts with same emails
    for i, account in enumerate(accounts):
        for email in account[1:]:
            if email in email_to_id:
                uf.union(i, email_to_id[email])
            else:
                email_to_id[email] = i
    
    # Group emails by root
    id_to_emails = {}
    for i in range(len(accounts)):
        root = uf.find(i)
        if root not in id_to_emails:
            id_to_emails[root] = set()
        for email in accounts[i][1:]:
            id_to_emails[root].add(email)
    
    # Build result
    result = []
    for root, emails in id_to_emails.items():
        result.append([accounts[root][0]] + sorted(emails))
    
    return result

# Example:
accounts = [
    ["John", "johnsmith@mail.com", "john_newyork@mail.com"],
    ["John", "johnsmith@mail.com", "john00@mail.com"],
    ["Mary", "mary@mail.com"],
    ["John", "johnnybravo@mail.com"]
]
print(accounts_merge(accounts))
# Output: [
#   ["John", "john00@mail.com", "john_newyork@mail.com", "johnsmith@mail.com"],
#   ["Mary", "mary@mail.com"],
#   ["John", "johnnybravo@mail.com"]
# ]
```

---

## 🔁 Variants

- Union by size
- Lazy union (less eager compression)
- Persistent union-find (with rollback support)

---

