---
title: "Graph / Adjacency List Overview"
date: 2024-01-09
categories: [data-structures]
tags: [graph, graphs, adjacency-list, adjacency-matrix, data-structures, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, network, shortest-path, topological-sort, problem-solving, big-o, time-complexity, social-network, routing]
---

A graph is a collection of nodes connected by edges, used to model relationships and networks in computer science. This post introduces graph representations, key concepts, and provides practical Python examples and problems for understanding graphs and their applications.

# Graph / Adjacency List

## Overview

A **graph** is a set of **nodes (vertices)** connected by **edges**.

- Can be **directed** or **undirected**
- Can be **weighted** or **unweighted**

Graphs are commonly represented using:

- **Adjacency list** (efficient for sparse graphs)
- **Adjacency matrix** (efficient for dense graphs)

---

## 🧩 Visualizing a Graph

Suppose we have the following graph:

```python
graph = {
    'A': ['B', 'C'],
    'B': ['D'],
    'C': ['E'],
    'D': [],
    'E': ['D']
}
```

### Diagram

```mermaid
graph TD;
  A --> B;
  A --> C;
  B --> D;
  C --> E;
  E --> D;
```

- Nodes: A, B, C, D, E
- Edges: (A,B), (A,C), (B,D), (C,E), (E,D)

---

## 🛠️ How to Use (Python)

```python
# Example of a directed, unweighted graph using an adjacency list (dictionary)
graph = {
    'A': ['B', 'C'],  # Node A points to B and C
    'B': ['D'],       # Node B points to D
    'C': ['E'],       # Node C points to E
    'D': [],          # Node D has no outgoing edges
    'E': ['D']        # Node E points to D
}
# Access neighbors: graph['A'] returns ['B', 'C']
# All operations above are O(1) for lookup, O(k) for iterating neighbors

# Example:
print(graph['A'])  # Output: ['B', 'C']
```

---

## 🧩 Cycle Detection Flow

Suppose we run cycle detection on the above graph. The DFS stack and visited set change as follows:

| Step | Node | rec_stack      | visited        | Cycle Detected? |
|------|------|---------------|----------------|-----------------|
| 1    | A    | {A}           | {A}            | No              |
| 2    | B    | {A, B}        | {A, B}         | No              |
| 3    | D    | {A, B, D}     | {A, B, D}      | No              |
| 4    | B    | {A}           | {A, B, D}      | No              |
| 5    | C    | {A, C}        | {A, B, C, D}   | No              |
| 6    | E    | {A, C, E}     | {A, B, C, D, E}| No              |
| 7    | D    | {A, C, E, D}  | {A, B, C, D, E}| No              |
| 8    | E    | {A, C}        | {A, B, C, D, E}| No              |
| 9    | C    | {A}           | {A, B, C, D, E}| No              |
| 10   | A    | {}            | {A, B, C, D, E}| No              |

- No cycle is detected in this example.

---

## 📘 Sample Problem 1: Detect Cycle in Directed Graph

> Return True if the graph has a cycle.

```python
def has_cycle(graph):
    visited = set()
    rec_stack = set()

    def dfs(node):
        # If node is in recursion stack, a cycle is found
        if node in rec_stack:
            return True
        # If already visited, skip
        if node in visited:
            return False
        visited.add(node)
        rec_stack.add(node)
        for neighbor in graph[node]:
            if dfs(neighbor):
                return True
        rec_stack.remove(node)
        return False

    for node in graph:
        if dfs(node):
            return True
    return False

# Example:
graph = {
    'A': ['B', 'C'],
    'B': ['D'],
    'C': ['E'],
    'D': [],
    'E': ['D']
}
print(has_cycle(graph))  # Output: False
```

---

## 🔁 Variants

- Weighted graphs (store edge weights as tuples or dict)
- Bidirectional graphs (undirected)
- Grid graphs (2D neighbors)
- Disjoint-set (union-find structure)

---
