---
title: "HashMap / Dictionary Overview"
date: 2023-08-10
categories: [data-structures]
tags: [hashmap, dictionary, python]
---

A HashMap (or dictionary in Python) is a data structure that maps keys to values for fast lookup, insertion, and deletion. This post covers the fundamentals of hashmaps, their use cases, and practical examples in Python, including common operations and sample problems.

## ✅ What It Is

A **HashMap** (or `dict` in Python) is a data structure that maps **keys to values** for fast lookup, insertion, and deletion — typically in **O(1)** average time.

- Also known as hash tables
- Keys must be hashable (immutable types like int, str, tuple)

## 🛠️ How to Use (Python)

```python
# Basic dictionary (hashmap) operations in Python
# - Create a dictionary, add key-value pairs, access values, get with default, remove by key
d = {}
d["name"] = "Alice"           # Add a key-value pair
d["name"]                      # Access value for key 'name' (returns 'Alice')
d.get("age", 0)                # Get value for 'age', return 0 if not found
d.pop("name")                  # Remove key 'name' and return its value
# All operations above are O(1) on average
```

## 📦 Use Cases

- Frequency counting
- Caching / memoization
- Lookup tables
- Grouping data
- Building graphs (adjacency list)

## 📘 Sample Problem 1: First Unique Character

> Given a string, return the index of the first non-repeating character.

```python
# This function finds the index of the first character that appears only once in the string.
def first_uniq_char(s):
    count = {}  # Dictionary to count occurrences of each character
    for ch in s:
        count[ch] = count.get(ch, 0) + 1  # Count each character

    for i, ch in enumerate(s):
        if count[ch] == 1:
            return i  # Return the index of the first unique character
    return -1  # If no unique character found
# Time complexity: O(n), Space complexity: O(1) (since alphabet size is limited)
```

## 📘 Sample Problem 2: Group Anagrams

> Group strings that are anagrams of each other.

```python
from collections import defaultdict

# This function groups words that are anagrams into lists.
def group_anagrams(strs):
    groups = defaultdict(list)  # Dictionary to group anagrams
    for word in strs:
        key = tuple(sorted(word))  # Sort the word to use as a key
        groups[key].append(word)   # Add word to the correct group
    return list(groups.values())   # Return all groups as a list
# Time complexity: O(n * k log k), where n = number of words, k = max word length
# Space complexity: O(n)
```

## 🔁 Variants

- OrderedDict (preserves order)
- defaultdict (auto-initialized)
- Counter (frequency maps)
- Two-key hash (nested dictionaries)

---

