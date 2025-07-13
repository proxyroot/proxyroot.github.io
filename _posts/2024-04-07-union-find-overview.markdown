---
title: "Union-Find / Disjoint Set Overview"
date: 2024-04-07
categories: [data-structures]
tags: [union-find, disjoint-set, python]
---

# Union-Find / Disjoint Set

## ✅ What It Is

A **disjoint set** (or **Union-Find**) is a data structure to keep track of a partition of elements into disjoint (non-overlapping) sets.

Supports two main operations efficiently:

- `find(x)`: returns the root/representative of the set that contains `x`
- `union(x, y)`: merges the sets containing `x` and `y`

With **path compression** and **union by rank**, both operations run in nearly constant time: `O(α(n))`.

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
            return False
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

- Detecting cycles in undirected graphs
- Kruskal’s algorithm (minimum spanning tree)
- Connected components in networks
- Dynamic connectivity problems

## 📘 Sample Problem 1: Number of Connected Components

> Given `n` nodes and a list of undirected edges, return the number of connected components.

```python
def count_components(n, edges):
    uf = UnionFind(n)
    for u, v in edges:
        uf.union(u, v)
    return len(set(uf.find(i) for i in range(n)))
```

## 📘 Sample Problem 2: Accounts Merge

> Merge email accounts that belong to the same user (based on overlapping emails).

## 🔁 Variants

- Union by size
- Lazy union (less eager compression)
- Persistent union-find (with rollback support)

---

