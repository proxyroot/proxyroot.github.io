---
title: "Set Overview"
date: 2023-09-05
categories: [data-structures]
tags: [set, sets, data-structures, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, hash-table, membership, problem-solving, big-o, time-complexity]
---

A set is a collection of unique, unordered elements that supports fast membership testing and set operations. This post introduces sets in Python, their properties, common operations, and provides practical examples and problems for understanding their use cases.

# Set

## ✅ What It Is

A **set** is a collection of **unique**, unordered elements. In Python, it is implemented using a hash table.

- No duplicate elements
- Fast lookup, add, and remove (average **O(1)** time)

## 🛠️ How to Use (Python)

```python
# Basic set operations in Python
# - Create a set, add/remove elements, check membership, set operations
s = set()
s.add(3)                # Add 3 to the set
s.add(5)                # Add 5 to the set
s.remove(3)             # Remove 3 from the set
print(5 in s)           # Check if 5 is in the set (prints True)
s2 = {1, 2, 5}
s.union(s2)             # Union: {1, 2, 5, 9, 3, 4}
s.intersection(s2)      # Intersection: {5}
# Most set operations are O(1) on average
```

## 📦 Use Cases

- Membership testing
- Removing duplicates
- Set operations (union, intersection, difference)
- Finding common or unique items
- Keeping visited nodes in graphs

## 📘 Sample Problem 1: Contains Duplicate

> Given a list of numbers, return True if any value appears at least twice.

```python
# This function checks if there are any duplicate numbers in the list.
def contains_duplicate(nums):
    seen = set()  # Set to keep track of seen numbers
    for num in nums:
        if num in seen:
            return True  # Found a duplicate
        seen.add(num)
    return False  # No duplicates found
# Time complexity: O(n), Space complexity: O(n)
```

## 📘 Sample Problem 2: Longest Consecutive Sequence

> Given an unsorted array, find the length of the longest consecutive elements sequence.

```python
# This function finds the length of the longest sequence of consecutive numbers in the list.
def longest_consecutive(nums):
    num_set = set(nums)  # Convert list to set for O(1) lookups
    longest = 0

    for num in num_set:
        if num - 1 not in num_set:  # Start of a sequence
            length = 1
            while num + length in num_set:
                length += 1
            longest = max(longest, length)

    return longest
# Time complexity: O(n), Space complexity: O(n)
```

## 🔁 Variants

- FrozenSet (immutable set)
- Set of tuples or other hashable objects
- Used in union-find / disjoint set problems

---

