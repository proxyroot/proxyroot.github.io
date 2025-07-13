---
title: "Set Overview"
date: 2023-09-05
categories: [data-structures]
tags: [set, python]
---

# Set

## ✅ What It Is

A **set** is a collection of **unique**, unordered elements. In Python, it is implemented using a hash table.

- No duplicate elements
- Fast lookup, add, and remove (average **O(1)** time)

## 🛠️ How to Use (Python)

```python
s = set()
s.add(3)
s.add(5)
s.remove(3)
print(5 in s)     # True
s2 = {1, 2, 5}
s.union(s2)       # {2, 5, 1}
s.intersection(s2) # {5}
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
def contains_duplicate(nums):
    seen = set()
    for num in nums:
        if num in seen:
            return True
        seen.add(num)
    return False
```

## 📘 Sample Problem 2: Longest Consecutive Sequence

> Given an unsorted array, find the length of the longest consecutive elements sequence.

```python
def longest_consecutive(nums):
    num_set = set(nums)
    longest = 0

    for num in num_set:
        if num - 1 not in num_set:  # start of a sequence
            length = 1
            while num + length in num_set:
                length += 1
            longest = max(longest, length)

    return longest
```

## 🔁 Variants

- FrozenSet (immutable set)
- Set of tuples or other hashable objects
- Used in union-find / disjoint set problems

---

