---
title: "Trie (Prefix Tree) Overview"
date: 2024-02-14
categories: [data-structures]
tags: [trie, prefix-tree, suffix-trie, radix-tree, data-structures, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, autocomplete, spell-check, string-search, problem-solving, big-o, time-complexity, word-games, ip-routing]
---

A Trie (prefix tree) is a tree-like data structure optimized for storing and searching strings, especially for prefix-based operations. This post covers the basics of Tries, their use cases, and provides practical Python examples and problems for string manipulation and search.

## Overview
A **Trie** is a tree-like data structure used to efficiently store and retrieve **strings**, especially useful for **prefix-based operations**.

- Each node represents a character
- Paths down the tree represent words
- Efficient for autocomplete, dictionary matching, etc.

## 🧩 Visualizing Tries

### Sample Trie Structure

```mermaid
graph TD
    subgraph "Trie for words: cat, car, dog, dot"
        Root[root] --> C[c]
        Root --> D[d]
        
        C --> A[a]
        D --> O[o]
        
        A --> T1[t*]
        A --> R[r*]
        O --> G[g]
        
        G --> T2[t*]
    end
    
    subgraph "Word Paths"
        P1[cat: root → c → a → t*]
        P2[car: root → c → a → r*]
        P3[dog: root → d → o → g]
        P4[dot: root → d → o → g → t*]
    end
    
    style T1 fill:#ff9999
    style R fill:#ff9999
    style T2 fill:#ff9999
```

### Trie Operations

```mermaid
graph TD
    I1[Insert cat: root to c to a to t* success]
    I2[Insert car: root to c to a to r* success]
    
    S1[Search cat: root to c to a to t* True]
    S2[Search ca: root to c to a False]
    S3[StartsWith ca: root to c to a True]
    
    Op1[Insert] --> Op2[Create nodes]
    Op2 --> Op3[Mark end]
    
    Op4[Search] --> Op5[Follow path]
    Op5 --> Op6[Check is_end]
    
    Op7[StartsWith] --> Op8[Follow path]
    Op8 --> Op9[Return True]
    
    style I1 fill:#99ff99
    style S1 fill:#99ccff
    style S3 fill:#99ccff
```

---

## 🛠️ How to Use (Python)

```python
# Implementation of a Trie (Prefix Tree) in Python
class TrieNode:
    def __init__(self):
        self.children = {}  # Dictionary mapping char to TrieNode
        self.is_end = False # True if node represents end of a word

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()  # Create node if not present
            node = node.children[char]
        node.is_end = True  # Mark end of word

    def search(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                return False  # Char not found
            node = node.children[char]
        return node.is_end  # True if end of word

    def starts_with(self, prefix):
        node = self.root
        for char in prefix:
            if char not in node.children:
                return False  # Prefix not found
            node = node.children[char]
        return True  # Prefix exists
# All operations above are O(L), where L is the length of the word/prefix

# Example:
trie = Trie()
trie.insert("cat")
trie.insert("car")
print(trie.search("cat"))      # Output: True
print(trie.search("ca"))       # Output: False
print(trie.starts_with("ca"))  # Output: True
```

---

## 🧩 Trie Operations Step-by-Step

```mermaid
graph TD
    Start[Start: Empty trie]
    InsertCat[Insert cat: root to c to a to t*]
    InsertCar[Insert car: root to c to a to r*]
    InsertDog[Insert dog: root to d to o to g*]
    Final[Final trie with 3 words]

    Start --> InsertCat --> InsertCar --> InsertDog --> Final
    style Final fill:#99ff99
```

Suppose we insert ["cat", "car", "dog"] into an empty trie:

| Operation | word | char | node.children | action | is_end |
|-----------|------|------|---------------|--------|--------|
| Insert    | "cat"| c    | {}            | Create c | False |
| Insert    | "cat"| a    | {c}           | Create a | False |
| Insert    | "cat"| t    | {c: {a}}      | Create t | True  |
| Insert    | "car"| c    | {c: {a: {t}}} | Use c   | False |
| Insert    | "car"| a    | {c: {a: {t}}} | Use a   | False |
| Insert    | "car"| r    | {c: {a: {t,r}}} | Create r | True |
| Insert    | "dog"| d    | {c,d}         | Create d | False |
| Insert    | "dog"| o    | {c: {...}, d} | Create o | False |
| Insert    | "dog"| g    | {c: {...}, d: {o}} | Create g | True |

---

## 🧩 Replace Words Flow

```mermaid
graph TD
    Start[Start: sentence = the cattle was rattled by the battery]
    The[the: no match keep the]
    Cattle[cattle: prefix cat found replace with cat]
    Was[was: no match keep was]
    Rattled[rattled: prefix rat found replace with rat]
    By[by: no match keep by]
    The2[the: no match keep the]
    Battery[battery: prefix bat found replace with bat]
    Result[Result: the cat was rat by the bat]

    Start --> The --> Cattle --> Was --> Rattled --> By --> The2 --> Battery --> Result
    style Result fill:#99ff99
```

Suppose dictionary = ["cat", "bat", "rat"], sentence = "the cattle was rattled by the battery"

| word | char | prefix | node.children | node.is_end | action |
|------|------|--------|---------------|-------------|--------|
| "the" | t | "" | {} | False | No match, return "the" |
| "cattle" | c | "c" | {c} | False | Continue |
| "cattle" | a | "ca" | {c: {a}} | False | Continue |
| "cattle" | t | "cat" | {c: {a: {t}}} | True | Return "cat" |
| "was" | w | "" | {} | False | No match, return "was" |
| "rattled" | r | "r" | {r} | False | Continue |
| "rattled" | a | "ra" | {r: {a}} | False | Continue |
| "rattled" | t | "rat" | {r: {a: {t}}} | True | Return "rat" |

- Final result: "the cat was rat by the bat"

---

## 📦 Use Cases
- Autocomplete engines
- Spell checkers
- Word games / scrabble solvers
- IP routing (longest prefix match)

## 📘 Sample Problem 1: Word Search II
> Given a board of letters and a list of words, find all that exist on the board using DFS + Trie.

## 📘 Sample Problem 2: Replace Words

> Replace words in a sentence with their shortest root in a given dictionary.

```python
def replace_words(dictionary, sentence):
    trie = Trie()
    for word in dictionary:
        trie.insert(word)

    def replace(word):
        node = trie.root
        prefix = ""
        for char in word:
            if char not in node.children or node.is_end:
                break
            prefix += char
            node = node.children[char]
        return prefix if node.is_end else word

    return ' '.join(replace(w) for w in sentence.split())

# Example:
dictionary = ["cat", "bat", "rat"]
sentence = "the cattle was rattled by the battery"
print(replace_words(dictionary, sentence))  # Output: "the cat was rat by the bat"
```

## 🔁 Variants
- Compressed Trie (Radix Tree)
- Suffix Trie (for substring search)
- Ternary Search Trie (for space optimization)

---
