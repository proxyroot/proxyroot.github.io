---
title: "Union-Find (Disjoint Set)"
date: 2025-05-12
categories: [data-structures]
tags: [union-find, disjoint-set, python]
---

A **Union-Find** (also called **Disjoint Set Union**, or **DSU**) is a data structure that efficiently manages a collection of disjoint (non-overlapping) sets. It supports two main operations: **finding** which set a particular element belongs to, and **uniting** (merging) two sets together. Union-Find is widely used in algorithms that need to track connectivity or groupings, such as in network connectivity, Kruskal’s algorithm for minimum spanning trees, and cycle detection in graphs. With optimizations like **path compression** and **union by rank**, both operations can be performed in nearly constant time.

## ✅ What It Is

**Union-Find**, or **Disjoint Set Union (DSU)**, is a data structure that keeps track of elements partitioned into disjoint sets and supports two operations efficiently:

- `find(x)`: Find the representative (root) of the set that contains `x`
- `union(x, y)`: Merge the sets containing `x` and `y`

Useful for **connectivity**, **component grouping**, and **cycle detection** in graphs.

## 🛠️ How to Use (Python)

```python
class UnionFind:
    def __init__(self, size):
        self.parent = list(range(size))
        self.rank = [0] * size

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # path compression
        return self.parent[x]

    def union(self, x, y):
        rootX = self.find(x)
        rootY = self.find(y)
        if rootX == rootY:
            return False  # already in same set

        # union by rank
        if self.rank[rootX] < self.rank[rootY]:
            self.parent[rootX] = rootY
        elif self.rank[rootX] > self.rank[rootY]:
            self.parent[rootY] = rootX
        else:
            self.parent[rootY] = rootX
            self.rank[rootX] += 1
        return True
```

## 📦 Use Cases

- Kruskal’s algorithm for Minimum Spanning Tree
- Cycle detection in undirected graphs
- Counting connected components
- Dynamic connectivity problems

## 📘 Sample Problem 1: Number of Connected Components in Graph

> Given `n` nodes and a list of undirected edges, return number of connected components.

```python
def count_components(n, edges):
    uf = UnionFind(n)
    for u, v in edges:
        uf.union(u, v)
    return len(set(uf.find(i) for i in range(n)))
```

## 📘 Sample Problem 2: Redundant Connection

> Find the edge that creates a cycle in a graph (first one if added).

```python
def find_redundant_connection(edges):
    n = len(edges)
    uf = UnionFind(n + 1)  # 1-based indexing
    for u, v in edges:
        if not uf.union(u, v):
            return [u, v]
```

## 🔁 Variants

- Weighted union heuristic
- Union-find with rollback (for offline queries)
- 2D union-find (for islands/flood fill)

---

