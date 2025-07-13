---
title: "Graph / Adjacency List Overview"
date: 2024-01-09
categories: [data-structures]
tags: [graph, adjacency-list, python]
---

A graph is a collection of nodes connected by edges, used to model relationships and networks in computer science. This post introduces graph representations, key concepts, and provides practical Python examples and problems for understanding graphs and their applications.

# Graph / Adjacency List

## ✅ What It Is

A **graph** is a set of **nodes (vertices)** connected by **edges**.

- Can be **directed** or **undirected**
- Can be **weighted** or **unweighted**

Graphs are commonly represented using:

- **Adjacency list** (efficient for sparse graphs)
- **Adjacency matrix** (efficient for dense graphs)

## 🛠️ How to Use (Python)

```python
# Directed unweighted graph (adjacency list)
graph = {
    'A': ['B', 'C'],
    'B': ['D'],
    'C': ['E'],
    'D': [],
    'E': ['D']
}
```

## 📦 Use Cases

- Navigation (Google Maps)
- Social networks (friends/followers)
- Scheduling and dependencies
- Network routing / data flow

## 📘 Sample Problem 1: Detect Cycle in Directed Graph

> Return True if the graph has a cycle.

```python
def has_cycle(graph):
    visited = set()
    rec_stack = set()

    def dfs(node):
        if node in rec_stack:
            return True
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
```

## 📘 Sample Problem 2: Topological Sort

> Return a topological ordering of a directed acyclic graph (DAG).

```python
def topological_sort(graph):
    visited = set()
    result = []

    def dfs(node):
        if node in visited:
            return
        visited.add(node)
        for neighbor in graph[node]:
            dfs(neighbor)
        result.append(node)

    for node in graph:
        dfs(node)

    return result[::-1]  # reverse postorder
```

## 🔁 Variants

- Weighted graphs (store edge weights as tuples or dict)
- Bidirectional graphs (undirected)
- Grid graphs (2D neighbors)
- Disjoint-set (union-find structure)

---

