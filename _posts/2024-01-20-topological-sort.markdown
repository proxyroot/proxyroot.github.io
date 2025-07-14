---
title: "Topological Sort (DAGs)"
date: 2024-01-20
categories: [algorithms]
tags: [topological-sort, dag, python]
---

Topological Sort is a method for ordering the nodes of a directed acyclic graph (DAG) so that each node comes before its dependencies. This post introduces topological sorting, its applications, and provides practical Python examples and problems for mastering DAG algorithms.

--- 

## What is Topological Sort? (Beginner-Friendly Explanation)

**Topological sort** is an ordering of the nodes in a directed graph such that for every directed edge `u → v`, node `u` comes before node `v` in the ordering.

### When is it used?
- **Only for Directed Acyclic Graphs (DAGs)** (no cycles!)
- Common in:
  - Task scheduling (some tasks must be done before others)
  - Course prerequisites (take course A before B)
  - Build systems (compile dependencies before dependents)

### Real-World Analogy

Imagine you’re baking a cake:
- You must **mix ingredients** before you **bake the cake**.
- You must **bake the cake** before you **decorate it**.
- You must **decorate** before you **serve**.

A valid order:  
`mix → bake → decorate → serve`

### Visual Example

Suppose you have these dependencies:
- A → C (A before C)
- B → C (B before C)
- C → D (C before D)

A possible topological order:  
`A, B, C, D`  
or  
`B, A, C, D`

```mermaid
flowchart LR
    A((A)) --> C((C))
    B((B)) --> C
    C --> D((D))
```

### Key Properties

- There can be **multiple valid topological orders** for a given DAG.
- If the graph has a cycle, **topological sort is impossible**.

---

## How Does the Algorithm Work?

**Kahn’s Algorithm (BFS-based):**
1. Compute the in-degree (number of incoming edges) for each node.
2. Start with nodes that have in-degree 0 (no dependencies).
3. Remove them from the graph, add to the result, and decrease the in-degree of their neighbors.
4. Repeat until all nodes are processed.

**DFS-based Algorithm:**
- Visit each node, recursively visit all its neighbors, and add the node to the result after visiting all its dependencies (post-order).

---

## Sample Code (Kahn’s Algorithm)

```python
from collections import deque, defaultdict

def topological_sort(graph):
    in_degree = defaultdict(int)
    for u in graph:
        for v in graph[u]:
            in_degree[v] += 1

    # Start with nodes with in-degree 0
    queue = deque([u for u in graph if in_degree[u] == 0])
    result = []

    while queue:
        u = queue.popleft()
        result.append(u)
        for v in graph[u]:
            in_degree[v] -= 1
            if in_degree[v] == 0:
                queue.append(v)

    if len(result) != len(graph):
        return "Cycle detected! No topological order."
    return result

# Example usage:
graph = {
    'A': ['C'],
    'B': ['C'],
    'C': ['D'],
    'D': []
}
print(topological_sort(graph))  # Output: ['A', 'B', 'C', 'D'] or ['B', 'A', 'C', 'D']
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

