---
title: "Bitmask DP Overview"
date: 2023-04-11
categories: [algorithms]
tags: [dp, bitmask, optimization, subsets]
---

Bitmask Dynamic Programming (Bitmask DP) is a powerful optimization technique for problems involving subsets or permutations, especially when brute-force is too slow. This post explains how bitmasks encode state, where Bitmask DP shines, and provides a well-commented example for beginners.

---

## What is Bitmask DP?

Bitmask DP uses integers (binary representations) to encode the state of a subset. Each bit in an integer represents whether an element is included (`1`) or not (`0`).

- Good for problems with **n <= 20**
- Used in **TSP, subset sum, scheduling, assignment** problems

---

## Example Use Case: Traveling Salesman Problem (TSP)

> Find the shortest tour that visits every city once and returns to the origin.

```python
import math

# Bitmask DP for TSP (Traveling Salesman Problem)
# Time Complexity: O(n^2 * 2^n), Space Complexity: O(n * 2^n)
def tsp(dist):
    n = len(dist)
    # dp[mask][i]: min cost to reach node i having visited nodes in mask
    dp = [[math.inf] * n for _ in range(1 << n)]
    dp[1][0] = 0  # Start at node 0 with only 0 visited

    for mask in range(1 << n):  # Iterate over all subsets
        for u in range(n):  # End at node u
            if not (mask & (1 << u)):
                continue
            for v in range(n):  # Try to go to node v
                if mask & (1 << v) or u == v:
                    continue
                next_mask = mask | (1 << v)
                # Update cost if visiting v next
                dp[next_mask][v] = min(
                    dp[next_mask][v],
                    dp[mask][u] + dist[u][v]
                )

    # Return to start node (0)
    return min(
        dp[(1 << n) - 1][i] + dist[i][0] for i in range(1, n)
    )

# Approach:
# - Use a bitmask to represent visited nodes (e.g., 01101 means nodes 0,2,3 visited).
# - dp[mask][i] = min cost to reach node i with nodes in mask visited.
# - Try all possible next nodes and update the DP table.
# - At the end, return to the starting node and find the minimum cost.
```

---

## Use Cases

- Traveling Salesman Problem (TSP)
- Assignment problems
- Subset sum with constraints
- Counting valid sequences with restrictions

---

## Variants

- Count number of ways using bitmasks
- Memoized DFS + bitmask instead of table DP
- Bitmask with constraints (e.g., prerequisites)

---

Bitmask DP is like a cheat code when solving subset-based problems — compact, elegant, and powerful for small sizes. 