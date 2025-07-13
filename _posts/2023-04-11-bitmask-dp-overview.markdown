---
title: "Bitmask Dynamic Programming (DP) Overview"
date: 2023-04-11
categories: [algorithms]
tags: [dp, bitmask, dynamic-programming, optimization, subsets, traveling-salesman, tsp, state-compression, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, problem-solving, big-o, time-complexity, bit-manipulation, combinatorics]
---

Bitmask Dynamic Programming is a powerful technique that uses binary representations to efficiently solve problems involving subsets, permutations, and state tracking. This post explains bitmasks, their applications, and provides detailed examples with visualizations to master this advanced DP technique.

## Overview

**Bitmask DP** uses integers (binary representations) to encode the state of a subset. Each bit in an integer represents whether an element is included (`1`) or not (`0`).

- **Best for**: Problems with **n ≤ 20** elements
- **Common applications**: TSP, subset sum, scheduling, assignment problems
- **Key advantage**: Compact state representation and efficient transitions

---

## 🧩 Understanding Bitmasks

### What is a Bitmask?

A bitmask is an integer where each bit represents a state:

```mermaid
graph TD
    Mask[Bitmask: 13 = 1101 binary]
    Bit0[Bit 0: 1 = Element 0 included]
    Bit1[Bit 1: 0 = Element 1 excluded]
    Bit2[Bit 2: 1 = Element 2 included]
    Bit3[Bit 3: 1 = Element 3 included]
    
    Mask --> Bit0
    Mask --> Bit1
    Mask --> Bit2
    Mask --> Bit3
    
    style Mask fill:#ff9999
    style Bit0 fill:#99ff99
    style Bit2 fill:#99ff99
    style Bit3 fill:#99ff99
    style Bit1 fill:#ff9999
```

### Bitmask Operations

```python
# Common bitmask operations
mask = 0b1101  # 13 in decimal

# Check if bit i is set
def is_set(mask, i):
    return (mask & (1 << i)) != 0

# Set bit i
def set_bit(mask, i):
    return mask | (1 << i)

# Clear bit i
def clear_bit(mask, i):
    return mask & ~(1 << i)

# Toggle bit i
def toggle_bit(mask, i):
    return mask ^ (1 << i)

# Count set bits
def count_bits(mask):
    return bin(mask).count('1')

# Example:
print(is_set(13, 0))    # True (bit 0 is 1)
print(is_set(13, 1))    # False (bit 1 is 0)
print(set_bit(13, 1))   # 15 (1101 | 0010 = 1111)
```

---

## 🧩 Visualizing Bitmask States

### Subset Representation

For 4 elements [A, B, C, D]:

```mermaid
graph TD
    Mask0[0000 = empty = 0]
    Mask1[0001 = A = 1]
    Mask2[0010 = B = 2]
    Mask3[0011 = A,B = 3]
    Mask4[0100 = C = 4]
    Mask5[0101 = A,C = 5]
    Mask6[0110 = B,C = 6]
    Mask7[0111 = A,B,C = 7]
    Mask8[1000 = D = 8]
    Mask9[1001 = A,D = 9]
    Mask10[1010 = B,D = 10]
    Mask11[1011 = A,B,D = 11]
    Mask12[1100 = C,D = 12]
    Mask13[1101 = A,C,D = 13]
    Mask14[1110 = B,C,D = 14]
    Mask15[1111 = A,B,C,D = 15]
    
    Mask0 --> Mask1 --> Mask2 --> Mask3
    Mask1 --> Mask5 --> Mask9 --> Mask13
    Mask2 --> Mask6 --> Mask10 --> Mask14
    Mask3 --> Mask7 --> Mask11 --> Mask15
    Mask4 --> Mask5 --> Mask6 --> Mask7
    Mask8 --> Mask9 --> Mask10 --> Mask11
    Mask12 --> Mask13 --> Mask14 --> Mask15
    
    style Mask0 fill:#f9f9f9
    style Mask15 fill:#99ff99
```

### State Transitions

```mermaid
graph TD
    Start[Start: mask = 0000 empty set]
    AddA[Add A: mask = 0001]
    AddB[Add B: mask = 0011]
    AddC[Add C: mask = 0111]
    AddD[Add D: mask = 1111 complete set]
    
    Start --> AddA --> AddB --> AddC --> AddD
    
    Op1[Operation: mask or 1 shift 0]
    Op2[Operation: mask or 1 shift 1]
    Op3[Operation: mask or 1 shift 2]
    Op4[Operation: mask or 1 shift 3]
    
    AddA -.-> Op1
    AddB -.-> Op2
    AddC -.-> Op3
    AddD -.-> Op4
    
    style Start fill:#f9f9f9
    style AddD fill:#99ff99
```

---

## 🛠️ How to Use (Python)

```python
# Basic bitmask operations for subset problems
def solve_with_bitmask(n):
    # Generate all possible subsets
    for mask in range(1 << n):  # 2^n subsets
        # Check which elements are included
        for i in range(n):
            if mask & (1 << i):  # Element i is included
                # Process element i
                pass
    
    # Example: Find all subsets of [1, 2, 3]
    elements = [1, 2, 3]
    for mask in range(1 << 3):
        subset = []
        for i in range(3):
            if mask & (1 << i):
                subset.append(elements[i])
        print(f"Mask {mask:03b} = {subset}")

# Output:
# Mask 000 = []
# Mask 001 = [1]
# Mask 010 = [2]
# Mask 011 = [1, 2]
# Mask 100 = [3]
# Mask 101 = [1, 3]
# Mask 110 = [2, 3]
# Mask 111 = [1, 2, 3]
```

---

## 🧩 Traveling Salesman Problem (TSP) Step-by-Step

### Problem: Find shortest tour visiting all cities once

```mermaid
graph TD
    Start[Start: 4 cities A,B,C,D]
    State1[State 1: Visit A only]
    State2[State 2: Visit A,B]
    State3[State 3: Visit A,B,C]
    State4[State 4: Visit A,B,C,D complete tour]
    
    Start --> State1 --> State2 --> State3 --> State4
    
    Mask1[mask=0001: only A visited]
    Mask2[mask=0011: A,B visited]
    Mask3[mask=0111: A,B,C visited]
    Mask4[mask=1111: all visited]
    
    State1 -.-> Mask1
    State2 -.-> Mask2
    State3 -.-> Mask3
    State4 -.-> Mask4
    
    style Start fill:#f9f9f9
    style State4 fill:#99ff99
```

### Distance Matrix Example

```
Cities: A(0), B(1), C(2), D(3)

Distance Matrix:
    A   B   C   D
A   0   2   9   10
B   2   0   6   4
C   9   6   0   3
D   10  4   3   0
```

### DP State Transitions

| Current State | Next City | New State | Cost Update |
|---------------|-----------|-----------|-------------|
| `dp[0001][0]` | Go to B   | `dp[0011][1]` | `dp[0001][0] + dist[0][1]` |
| `dp[0001][0]` | Go to C   | `dp[0101][2]` | `dp[0001][0] + dist[0][2]` |
| `dp[0001][0]` | Go to D   | `dp[1001][3]` | `dp[0001][0] + dist[0][3]` |
| `dp[0011][1]` | Go to C   | `dp[0111][2]` | `dp[0011][1] + dist[1][2]` |
| `dp[0011][1]` | Go to D   | `dp[1011][3]` | `dp[0011][1] + dist[1][3]` |

---

## 📘 Sample Problem 1: Traveling Salesman Problem (TSP)

> Find the shortest tour that visits every city once and returns to the origin.

```python
import math

def tsp_bitmask(dist):
    """
    Solve TSP using bitmask DP
    dist: distance matrix (n x n)
    Returns: minimum cost of complete tour
    """
    n = len(dist)
    
    # dp[mask][i]: minimum cost to reach city i having visited cities in mask
    dp = [[math.inf] * n for _ in range(1 << n)]
    
    # Base case: start at city 0 with only city 0 visited
    dp[1][0] = 0  # mask=0001, city=0, cost=0
    
    # Iterate over all possible subsets (masks)
    for mask in range(1 << n):
        # For each possible ending city
        for u in range(n):
            # Check if city u is visited in current mask
            if not (mask & (1 << u)):
                continue
            
            # Try to go to each unvisited city
            for v in range(n):
                # Skip if city v is already visited or same as u
                if mask & (1 << v) or u == v:
                    continue
                
                # New mask after visiting city v
                next_mask = mask | (1 << v)
                
                # Update minimum cost
                dp[next_mask][v] = min(
                    dp[next_mask][v],
                    dp[mask][u] + dist[u][v]
                )
    
    # Return to starting city (0) from any city
    final_mask = (1 << n) - 1  # All cities visited (1111...)
    return min(
        dp[final_mask][i] + dist[i][0] 
        for i in range(1, n)  # Don't include city 0 (already there)
    )

# Example usage:
distances = [
    [0, 2, 9, 10],
    [2, 0, 6, 4],
    [9, 6, 0, 3],
    [10, 4, 3, 0]
]

print(tsp_bitmask(distances))  # Output: 21 (A→B→D→C→A)
```

---

## 🧩 Subset Sum with Bitmask

### Problem: Find if any subset sums to target

```mermaid
graph TD
    Start[Start: nums=1,2,3 target=5]
    State1[State 1: Consider element 0]
    State2[State 2: Consider elements 0,1]
    State3[State 3: Consider elements 0,1,2]
    Found[Found: subset 2,3 sums to 5]
    
    Start --> State1 --> State2 --> State3 --> Found
    
    Mask1[mask=001: 1]
    Mask2[mask=011: 1,2]
    Mask3[mask=111: 1,2,3]
    
    State1 -.-> Mask1
    State2 -.-> Mask2
    State3 -.-> Mask3
    
    style Found fill:#99ff99
```

---

## 📘 Sample Problem 2: Subset Sum

> Check if any subset of the array sums to the target value.

```python
def subset_sum_bitmask(nums, target):
    """
    Check if any subset sums to target using bitmask
    nums: array of positive integers
    target: target sum
    Returns: True if subset exists, False otherwise
    """
    n = len(nums)
    
    # Try all possible subsets
    for mask in range(1 << n):
        subset_sum = 0
        
        # Calculate sum of current subset
        for i in range(n):
            if mask & (1 << i):  # Element i is included
                subset_sum += nums[i]
        
        # Check if this subset sums to target
        if subset_sum == target:
            # Print the subset for verification
            subset = [nums[i] for i in range(n) if mask & (1 << i)]
            print(f"Found subset: {subset}")
            return True
    
    return False

# Example:
nums = [1, 2, 3, 4, 5]
target = 7
print(subset_sum_bitmask(nums, target))  # True (subset [2,5] or [3,4])
```

---

## 🧩 Assignment Problem Flow

### Problem: Assign tasks to workers with minimum cost

```mermaid
graph TD
    Start[Start: 3 workers, 3 tasks]
    State1[State 1: Assign task 0 to any worker]
    State2[State 2: Assign task 1 to remaining workers]
    State3[State 3: Assign task 2 to last worker]
    Complete[Complete: All tasks assigned]
    
    Start --> State1 --> State2 --> State3 --> Complete
    
    Mask1[mask=001: worker 0 assigned]
    Mask2[mask=011: workers 0,1 assigned]
    Mask3[mask=111: all workers assigned]
    
    State1 -.-> Mask1
    State2 -.-> Mask2
    State3 -.-> Mask3
    
    style Complete fill:#99ff99
```

---

## 📘 Sample Problem 3: Assignment Problem

> Assign n tasks to n workers with minimum total cost.

```python
def assignment_bitmask(cost):
    """
    Solve assignment problem using bitmask DP
    cost: cost matrix (n x n) where cost[i][j] = cost of assigning task i to worker j
    Returns: minimum total cost
    """
    n = len(cost)
    
    # dp[mask]: minimum cost to assign tasks represented by mask
    dp = [float('inf')] * (1 << n)
    dp[0] = 0  # Base case: no tasks assigned
    
    # For each possible assignment state
    for mask in range(1 << n):
        # Count how many tasks are assigned
        task_count = bin(mask).count('1')
        
        # Try assigning task task_count to each worker
        for worker in range(n):
            # Check if worker is available (not assigned yet)
            if not (mask & (1 << worker)):
                # Assign task task_count to worker
                new_mask = mask | (1 << worker)
                dp[new_mask] = min(
                    dp[new_mask],
                    dp[mask] + cost[task_count][worker]
                )
    
    return dp[(1 << n) - 1]  # All tasks assigned

# Example:
cost_matrix = [
    [3, 2, 1],  # Task 0 costs
    [4, 1, 2],  # Task 1 costs
    [2, 3, 1]   # Task 2 costs
]

print(assignment_bitmask(cost_matrix))  # Output: 4 (optimal assignment)
```

---

## 🔁 Common Patterns and Optimizations

### 1. Memoization with Bitmask

```python
from functools import lru_cache

@lru_cache(maxsize=None)
def solve_with_memo(mask, pos):
    """Memoized bitmask DP"""
    if mask == (1 << n) - 1:  # All elements processed
        return 0
    
    # Try all possible next moves
    for next_pos in range(n):
        if not (mask & (1 << next_pos)):
            new_mask = mask | (1 << next_pos)
            # Recursive call with memoization
            result = solve_with_memo(new_mask, next_pos)
            # Process result...
    
    return best_result
```

### 2. State Compression

```python
# Instead of storing full state, compress to bitmask
def compress_state(visited, current_pos):
    """Compress state to bitmask"""
    mask = 0
    for i, v in enumerate(visited):
        if v:
            mask |= (1 << i)
    return mask, current_pos
```

### 3. Pruning Invalid States

```python
# Skip states that can't lead to optimal solution
def is_valid_state(mask, additional_constraints):
    """Check if state is valid"""
    # Add your validation logic here
    return True
```

---

## 📊 Complexity Analysis

| Problem Type | Time Complexity | Space Complexity | When to Use |
|--------------|----------------|------------------|-------------|
| TSP | O(n² × 2ⁿ) | O(n × 2ⁿ) | n ≤ 20 |
| Subset Sum | O(n × 2ⁿ) | O(2ⁿ) | n ≤ 20 |
| Assignment | O(n² × 2ⁿ) | O(2ⁿ) | n ≤ 20 |
| Permutation | O(n! × n) | O(n!) | n ≤ 10 |

---

## 🎯 Key Takeaways

1. **Bitmasks encode subsets efficiently** - Each bit represents inclusion/exclusion
2. **State transitions are fast** - Use bitwise operations (|, &, ^, <<)
3. **Memory efficient** - 2ⁿ states for n elements
4. **Best for small n** - Typically n ≤ 20 for practical use
5. **Common applications** - TSP, assignment, subset problems

---

## 🔁 Variants

- **Count valid configurations** - Use bitmask to count instead of optimize
- **Multiple constraints** - Combine bitmasks for different constraints
- **Rolling bitmasks** - For sliding window problems
- **Bitmask + other DP** - Combine with other DP techniques

---

Bitmask DP is a powerful technique that transforms complex subset problems into efficient integer operations. Master the bitwise operations and state transitions, and you'll have a powerful tool for solving optimization problems with small state spaces! 