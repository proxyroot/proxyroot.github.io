---
title: "HashMap / Dictionary Overview"
date: 2023-08-10
categories: [data-structures]
tags: [hashmap, dictionary, hash-table, data-structures, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, key-value, problem-solving, big-o, time-complexity]
---

A HashMap (or dictionary in Python) is a data structure that maps keys to values for fast lookup, insertion, and deletion. This post covers the fundamentals of hashmaps, their use cases, and practical examples in Python, including common operations and sample problems.

## Overview

A **HashMap** (or `dict` in Python) is a data structure that maps **keys to values** for fast lookup, insertion, and deletion — typically in **O(1)** average time.

- Also known as hash tables
- Keys must be hashable (immutable types like int, str, tuple)

## 🧩 Visualizing HashMaps

### Hash Table Structure

A hashmap stores key-value pairs in buckets:

```mermaid
graph TD
    subgraph "Hash Function: h(key) = key % 5"
        K1["name"] --> H1[Hash: 4]
        K2["age"] --> H2[Hash: 1]
        K3["city"] --> H3[Hash: 4]
    end
    
    subgraph "Hash Table Buckets"
        B0[Bucket 0<br/>Empty]
        B1[Bucket 1<br/>age: 25]
        B2[Bucket 2<br/>Empty]
        B3[Bucket 3<br/>Empty]
        B4[Bucket 4<br/>name: Alice<br/>city: NYC]
    end
    
    H1 --> B4
    H2 --> B1
    H3 --> B4
    
    style B4 fill:#ff9999
    style B1 fill:#99ccff
```

---

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

# Example:
d = {}
d["name"] = "Alice"
d["age"] = 25
print(d["name"])  # Output: Alice
print(d.get("city", "Unknown"))  # Output: Unknown
```

---

## 🧩 First Unique Character Step-by-Step

Suppose s = "leetcode"

| Step | ch | count[ch] | count dict                    | i | s[i] | count[s[i]] | Action |
|------|----|-----------|-------------------------------|---|------|-------------|--------|
| 1    | l  | 1         | {'l': 1}                      | 0 | l    | 1           | Continue|
| 2    | e  | 1         | {'l': 1, 'e': 1}              | 1 | e    | 1           | Continue|
| 3    | e  | 2         | {'l': 1, 'e': 2}              | 2 | e    | 2           | Continue|
| 4    | t  | 1         | {'l': 1, 'e': 2, 't': 1}      | 3 | t    | 1           | Continue|
| 5    | c  | 1         | {'l': 1, 'e': 2, 't': 1, 'c': 1}| 4 | c | 1           | Continue|
| 6    | o  | 1         | {'l': 1, 'e': 2, 't': 1, 'c': 1, 'o': 1}| 5 | o | 1 | Continue|
| 7    | d  | 1         | {'l': 1, 'e': 2, 't': 1, 'c': 1, 'o': 1, 'd': 1}| 6 | d | 1 | Continue|
| 8    | e  | 3         | {'l': 1, 'e': 3, 't': 1, 'c': 1, 'o': 1, 'd': 1}| 7 | e | 3 | Continue|

- Return 0 (index of 'l', the first unique character).

---

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

# Example:
s = "leetcode"
print(first_uniq_char(s))  # Output: 0 (index of 'l')
```

---

## 🧩 Group Anagrams Flow

Suppose strs = ["eat", "tea", "tan", "ate", "nat", "bat"]

1. For each word, create a sorted key:
   - "eat" → sorted("eat") = "aet"
   - "tea" → sorted("tea") = "aet"
   - "tan" → sorted("tan") = "ant"
   - "ate" → sorted("ate") = "aet"
   - "nat" → sorted("nat") = "ant"
   - "bat" → sorted("bat") = "abt"

2. Group by sorted key:
   - "aet": ["eat", "tea", "ate"]
   - "ant": ["tan", "nat"]
   - "abt": ["bat"]

---

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

# Example:
strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
print(group_anagrams(strs))  # Output: [['eat', 'tea', 'ate'], ['tan', 'nat'], ['bat']]
```

---

## 🔁 Variants

- OrderedDict (preserves order)
- defaultdict (auto-initialized)
- Counter (frequency maps)
- Two-key hash (nested dictionaries)

---

