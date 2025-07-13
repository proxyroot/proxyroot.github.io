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
d = {}
d["name"] = "Alice"
print(d["name"])   # Alice
d.get("age", 0)     # returns 0 if key doesn't exist
d.pop("name")       # remove key-value pair
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
def first_uniq_char(s):
    count = {}
    for ch in s:
        count[ch] = count.get(ch, 0) + 1

    for i, ch in enumerate(s):
        if count[ch] == 1:
            return i
    return -1
```

## 📘 Sample Problem 2: Group Anagrams

> Group strings that are anagrams of each other.

```python
from collections import defaultdict

def group_anagrams(strs):
    groups = defaultdict(list)
    for word in strs:
        key = tuple(sorted(word))
        groups[key].append(word)
    return list(groups.values())
```

## 🔁 Variants

- OrderedDict (preserves order)
- defaultdict (auto-initialized)
- Counter (frequency maps)
- Two-key hash (nested dictionaries)

---

