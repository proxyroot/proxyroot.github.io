---
title: "Topological Sort (DAGs)"
date: 2024-01-20
categories: [algorithms]
tags: [topological-sort, dag, python]
---

Topological Sort is a method for ordering the nodes of a directed acyclic graph (DAG) so that each node comes before its dependencies. This post introduces topological sorting, its applications, and provides practical Python examples and problems for mastering DAG algorithms.

## Overview

**Topological Sort** is a linear ordering of vertices such that for every directed edge `u -> v`, node `u` comes before `v` in the ordering.

- Works only on **Directed Acyclic Graphs (DAGs)**
- Useful for **dependency resolution**
- Two common approaches:
  - **DFS-based** (post-order stack)
  - **Kahn’s algorithm** (in-degree + BFS)

## 🛠️ Kahn’s Algorithm (Python)

```python
from collections import defaultdict, deque

# Kahn's algorithm for topological sort using in-degree and BFS
def topological_sort_kahn(vertices, edges):
    in_degree = {v: 0 for v in vertices}  # Initialize in-degree of all vertices
    graph = defaultdict(list)
    
    for u, v in edges:
        graph[u].append(v)
        in_degree[v] += 1  # Count incoming edges

    queue = deque([v for v in vertices if in_degree[v] == 0])  # Start with nodes with 0 in-degree
    order = []

    while queue:
        node = queue.popleft()
        order.append(node)
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)

    return order if len(order) == len(vertices) else []  # Return empty if cycle
# Time complexity: O(V + E), Space: O(V + E)
```

## 🛠️ DFS-based Topological Sort

```python
# Topological sort using DFS and post-order stack
def topological_sort_dfs(graph):
    visited = set()
    order = []

    def dfs(node):
        if node in visited:
            return
        visited.add(node)
        for neighbor in graph[node]:
            dfs(neighbor)
        order.append(node)  # Post-order

    for node in graph:
        if node not in visited:
            dfs(node)

    return order[::-1]  # Reverse post-order
# Time complexity: O(V + E), Space: O(V + E)
```

## 📦 Use Cases

- Build system dependency resolution (e.g. compiling files)
- Course schedule ordering
- Resolving package dependencies

## 📘 Sample Problem 1: Course Schedule II

> Return a valid order of courses to finish given prerequisites.

```python
from collections import defaultdict, deque

# Kahn's algorithm for topological sort using in-degree and BFS
def find_order(num_courses, prerequisites):
    graph = defaultdict(list)
    in_degree = [0] * num_courses
    for a, b in prerequisites:
        graph[b].append(a)
        in_degree[a] += 1

    queue = deque([i for i in range(num_courses) if in_degree[i] == 0])
    res = []

    while queue:
        node = queue.popleft()
        res.append(node)
        for nei in graph[node]:
            in_degree[nei] -= 1
            if in_degree[nei] == 0:
                queue.append(nei)

    return res if len(res) == num_courses else []
```

## 🔁 Variants

- Cycle detection (if no valid topo sort)
- Lexicographically smallest topological order
- Multi-source DAGs

---

