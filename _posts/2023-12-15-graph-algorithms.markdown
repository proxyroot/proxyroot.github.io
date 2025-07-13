---
title: "Graph Algorithms (DFS / BFS)"
date: 2023-12-15
categories: [algorithms]
tags: [graph, graphs, dfs, bfs, traversal, data-structures, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, shortest-path, connected-components, problem-solving, big-o, time-complexity, maze, topological-sort]
---

Graph algorithms such as Depth-First Search (DFS) and Breadth-First Search (BFS) are essential for traversing and analyzing graph structures. This post covers the basics of DFS and BFS, their use cases, and provides practical Python examples and problems for mastering graph traversal techniques.

## Overview

Graph algorithms like **DFS (Depth-First Search)** and **BFS (Breadth-First Search)** are used to traverse or search through graph data structures.

- DFS uses a **stack** or recursion
- BFS uses a **queue**

Both are used in problems involving connectivity, cycles, components, and shortest paths.

## 🛠️ DFS and BFS (Python)

```python
# Depth-First Search (DFS) - recursive
# Visits all nodes reachable from 'node' in a graph using recursion.
def dfs(graph, node, visited):
    if node in visited:
        return
    visited.add(node)  # Mark node as visited
    for neighbor in graph[node]:
        dfs(graph, neighbor, visited)  # Visit neighbors recursively
# Time complexity: O(V + E), Space: O(V) for visited set and recursion stack

# Breadth-First Search (BFS) - iterative
# Visits all nodes reachable from 'start' in a graph using a queue.
from collections import deque

def bfs(graph, start):
    visited = set()
    queue = deque([start])  # Start with the initial node
    while queue:
        node = queue.popleft()  # Remove from front of queue
        if node in visited:
            continue
        visited.add(node)  # Mark node as visited
        for neighbor in graph[node]:
            queue.append(neighbor)  # Add neighbors to queue
# Time complexity: O(V + E), Space: O(V) for visited set and queue
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
# This function counts the number of islands (connected groups of '1's) in a 2D grid.
def num_islands(grid):
    rows, cols = len(grid), len(grid[0])
    visited = set()

    def dfs(r, c):
        # Return if out of bounds, water, or already visited
        if (r < 0 or c < 0 or r >= rows or c >= cols or
            grid[r][c] == '0' or (r, c) in visited):
            return
        visited.add((r, c))  # Mark cell as visited
        # Visit all 4 directions
        dfs(r+1, c)
        dfs(r-1, c)
        dfs(r, c+1)
        dfs(r, c-1)

    count = 0
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1' and (r, c) not in visited:
                dfs(r, c)  # Start DFS for new island
                count += 1
    return count
# Time complexity: O(m*n), Space: O(m*n) for visited set and recursion stack
```

## 📘 Sample Problem 2: Shortest Path in Binary Matrix

> Return shortest path from top-left to bottom-right in grid (0=open, 1=blocked).

```python
from collections import deque

# This function finds the shortest path from (0,0) to (n-1,n-1) in a binary matrix using BFS.
def shortest_path_binary_matrix(grid):
    n = len(grid)
    if grid[0][0] or grid[n-1][n-1]:
        return -1  # Start or end is blocked

    directions = [(i, j) for i in [-1,0,1] for j in [-1,0,1] if i or j]  # 8 directions
    visited = set()
    queue = deque([(0, 0, 1)])  # (row, col, distance)

    while queue:
        r, c, dist = queue.popleft()
        if (r, c) == (n-1, n-1):
            return dist  # Reached end
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            if 0 <= nr < n and 0 <= nc < n and grid[nr][nc] == 0 and (nr, nc) not in visited:
                visited.add((nr, nc))
                queue.append((nr, nc, dist + 1))
    return -1  # No path found
# Time complexity: O(n^2), Space: O(n^2) for visited set and queue
```

## 🔁 Variants

- Bidirectional BFS
- Iterative DFS (using stack)
- Flood fill
- Topological sort (Kahn’s or DFS)

---

