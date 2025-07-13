---
title: "Graph Algorithms (DFS / BFS)"
date: 2023-12-15
categories: [algorithms]
tags: [graph, dfs, bfs, python]
---

# Graph Algorithms (DFS / BFS)

## ✅ What It Is

Graph algorithms like **DFS (Depth-First Search)** and **BFS (Breadth-First Search)** are used to traverse or search through graph data structures.

- DFS uses a **stack** or recursion
- BFS uses a **queue**

Both are used in problems involving connectivity, cycles, components, and shortest paths.

## 🛠️ DFS and BFS (Python)

```python
# DFS (recursive)
def dfs(graph, node, visited):
    if node in visited:
        return
    visited.add(node)
    for neighbor in graph[node]:
        dfs(graph, neighbor, visited)

# BFS (iterative)
from collections import deque

def bfs(graph, start):
    visited = set()
    queue = deque([start])
    while queue:
        node = queue.popleft()
        if node in visited:
            continue
        visited.add(node)
        for neighbor in graph[node]:
            queue.append(neighbor)
```

## 📦 Use Cases

- Cycle detection
- Shortest path in unweighted graph (BFS)
- Connected components
- Maze/board traversal
- Topological sort (with DFS)

## 📘 Sample Problem 1: Number of Islands

> Count the number of islands in a 2D grid (1 = land, 0 = water).

```python
def num_islands(grid):
    rows, cols = len(grid), len(grid[0])
    visited = set()

    def dfs(r, c):
        if (r < 0 or c < 0 or r >= rows or c >= cols or
            grid[r][c] == '0' or (r, c) in visited):
            return
        visited.add((r, c))
        dfs(r+1, c)
        dfs(r-1, c)
        dfs(r, c+1)
        dfs(r, c-1)

    count = 0
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1' and (r, c) not in visited:
                dfs(r, c)
                count += 1
    return count
```

## 📘 Sample Problem 2: Shortest Path in Binary Matrix

> Return shortest path from top-left to bottom-right in grid (0=open, 1=blocked).

```python
from collections import deque

def shortest_path_binary_matrix(grid):
    n = len(grid)
    if grid[0][0] or grid[n-1][n-1]:
        return -1

    directions = [(i, j) for i in [-1,0,1] for j in [-1,0,1] if i or j]
    visited = set()
    queue = deque([(0, 0, 1)])

    while queue:
        r, c, dist = queue.popleft()
        if (r, c) == (n-1, n-1):
            return dist
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            if 0 <= nr < n and 0 <= nc < n and grid[nr][nc] == 0 and (nr, nc) not in visited:
                visited.add((nr, nc))
                queue.append((nr, nc, dist + 1))
    return -1
```

## 🔁 Variants

- Bidirectional BFS
- Iterative DFS (using stack)
- Flood fill
- Topological sort (Kahn’s or DFS)

---

