---
title: "Dijkstra & Shortest Paths"
date: 2023-07-03
categories: [algorithms]
tags: [graph, dijkstra, shortest-path, greedy, heap]
---

Dijkstra’s Algorithm is a classic greedy algorithm for finding the shortest path from a source node to all other nodes in a weighted graph with non-negative edge weights. It uses a min-heap (priority queue) for efficiency and is widely used in routing and navigation. This post explains the algorithm, use cases, and provides annotated code for beginners.

---

## What is Dijkstra’s Algorithm?

Dijkstra’s Algorithm finds the shortest path from a source node to all other nodes in a weighted graph (with non-negative edge weights).

- Uses a **min-heap** (priority queue)
- Greedy approach
- Efficient for sparse graphs

---

## Dijkstra’s Algorithm (Python)

```python
import heapq
from collections import defaultdict

# Dijkstra's Algorithm: Find shortest paths from start node
# Time Complexity: O((V+E) log V), Space Complexity: O(V+E)
def dijkstra(n, edges, start):
    graph = defaultdict(list)
    for u, v, w in edges:
        graph[u].append((v, w))

    dist = [float('inf')] * n
    dist[start] = 0
    heap = [(0, start)]  # (distance, node)

    while heap:
        curr_dist, u = heapq.heappop(heap)
        if curr_dist > dist[u]:
            continue  # Skip if we already found a better path
        for v, weight in graph[u]:
            if dist[v] > dist[u] + weight:
                dist[v] = dist[u] + weight
                heapq.heappush(heap, (dist[v], v))

    return dist

# Example usage:
# n = 5
# edges = [(0, 1, 2), (0, 2, 4), (1, 2, 1), (1, 3, 7), (2, 4, 3), (3, 4, 1)]
# start = 0
# print(dijkstra(n, edges, start))  # Output: [0, 2, 3, 9, 6]
```
# Approach:
# - Build the graph as an adjacency list.
# - Use a min-heap to always expand the closest node.
# - Update distances if a shorter path is found.
# - Repeat until all nodes are processed.

---

## Use Cases

- Shortest path in road networks
- Network routing protocols
- Map services (Google Maps, etc.)

---

## Sample Problem: Network Delay Time

> Given times[i] = (u, v, w), find time for all nodes to receive signal from node K.

```python
import heapq
from collections import defaultdict

# Network Delay Time using Dijkstra's Algorithm
def network_delay_time(times, n, k):
    graph = defaultdict(list)
    for u, v, w in times:
        graph[u].append((v, w))

    dist = [float('inf')] * (n + 1)
    dist[k] = 0
    heap = [(0, k)]

    while heap:
        d, node = heapq.heappop(heap)
        for nei, wt in graph[node]:
            if dist[nei] > d + wt:
                dist[nei] = d + wt
                heapq.heappush(heap, (dist[nei], nei))

    res = max(dist[1:])
    return res if res < float('inf') else -1

# Example usage:
# times = [(2, 1, 1), (2, 3, 1), (3, 4, 1)]
# n = 4
# k = 2
# print(network_delay_time(times, n, k))  # Output: 2
```
# Approach:
# - Build the graph as an adjacency list.
# - Use a min-heap to expand the closest node.
# - Update distances as you find shorter paths.
# - The answer is the maximum distance from the source to any node.

---

## Variants

- Bellman-Ford (works with negative weights)
- Floyd-Warshall (all-pairs shortest paths)
- A* algorithm (with heuristic)

---

Dijkstra’s Algorithm is a must-know for efficient shortest path computation in graphs with non-negative weights. 