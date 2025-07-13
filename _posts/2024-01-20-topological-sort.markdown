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

---

## 🧩 Visualizing Topological Sort

### Sample DAG

```
Vertices: [A, B, C, D, E]
Edges: [(A,B), (A,C), (B,D), (C,D), (C,E), (D,E)]

Graph representation:
    A
   / \
  B   C
   \ / \
    D   E

In-degrees: A=0, B=1, C=1, D=2, E=2
```

### Topological Ordering

```
Valid orderings:
1. A → B → C → D → E
2. A → C → B → D → E
3. A → C → E → B → D

All satisfy: for every edge u→v, u comes before v
```

---

## 🛠️ Kahn's Algorithm (Python)

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

# Example:
vertices = ['A', 'B', 'C', 'D', 'E']
edges = [('A','B'), ('A','C'), ('B','D'), ('C','D'), ('C','E'), ('D','E')]
print(topological_sort_kahn(vertices, edges))  # Output: ['A', 'B', 'C', 'D', 'E']
```

---

## 🧩 Kahn's Algorithm Step-by-Step

Suppose vertices = ['A', 'B', 'C', 'D', 'E'], edges = [('A','B'), ('A','C'), ('B','D'), ('C','D'), ('C','E'), ('D','E')]

| Step | queue | node | order | in_degree updates | new queue |
|------|-------|------|-------|-------------------|-----------|
| 0    | [A]   | -    | []    | -                 | [A]       |
| 1    | [A]   | A    | [A]   | B:1→0, C:1→0      | [B,C]     |
| 2    | [B,C] | B    | [A,B] | D:2→1             | [C]       |
| 3    | [C]   | C    | [A,B,C] | D:1→0, E:2→1    | [D]       |
| 4    | [D]   | D    | [A,B,C,D] | E:1→0          | [E]       |
| 5    | [E]   | E    | [A,B,C,D,E] | -             | []        |

- Final result: ['A', 'B', 'C', 'D', 'E']

---

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

# Example:
graph = {
    'A': ['B', 'C'],
    'B': ['D'],
    'C': ['D', 'E'],
    'D': ['E'],
    'E': []
}
print(topological_sort_dfs(graph))  # Output: ['A', 'B', 'C', 'D', 'E']
```

---

## 🧩 DFS Topological Sort Flow

For the same graph, DFS traversal:

| Step | node | visited | neighbors | post_order | action |
|------|------|---------|-----------|------------|--------|
| 1    | A    | {A}     | [B,C]     | []         | dfs(B) |
| 2    | B    | {A,B}   | [D]       | []         | dfs(D) |
| 3    | D    | {A,B,D} | [E]       | []         | dfs(E) |
| 4    | E    | {A,B,D,E} | []      | [E]        | return |
| 5    | D    | {A,B,D,E} | [E]     | [E,D]      | return |
| 6    | B    | {A,B,D,E} | [D]     | [E,D,B]    | return |
| 7    | A    | {A,B,D,E} | [B,C]   | [E,D,B]    | dfs(C) |
| 8    | C    | {A,B,C,D,E} | [D,E] | [E,D,B]    | skip D, dfs(E) |
| 9    | E    | {A,B,C,D,E} | []    | [E,D,B]    | skip   |
| 10   | C    | {A,B,C,D,E} | [D,E] | [E,D,B,C]  | return |
| 11   | A    | {A,B,C,D,E} | [B,C] | [E,D,B,C,A] | return |

- Final result: ['A', 'C', 'B', 'D', 'E'] (reversed)

---

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

# Example:
num_courses = 4
prerequisites = [[1,0], [2,0], [3,1], [3,2]]
print(find_order(num_courses, prerequisites))  # Output: [0, 1, 2, 3]
```

---

## 🔁 Variants

- Cycle detection (if no valid topo sort)
- Lexicographically smallest topological order
- Multi-source DAGs

---

