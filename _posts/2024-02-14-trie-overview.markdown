---
title: "Trie (Prefix Tree) Overview"
date: 2024-02-14
categories: [data-structures]
tags: [trie, prefix-tree, suffix-trie, radix-tree, data-structures, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, autocomplete, spell-check, string-search, problem-solving, big-o, time-complexity, word-games, ip-routing]
---

A Trie (prefix tree) is a tree-like data structure optimized for storing and searching strings, especially for prefix-based operations. This post covers the basics of Tries, their use cases, and provides practical Python examples and problems for string manipulation and search.

## ✅ What It Is
A **Trie** is a tree-like data structure used to efficiently store and retrieve **strings**, especially useful for **prefix-based operations**.

- Each node represents a character
- Paths down the tree represent words
- Efficient for autocomplete, dictionary matching, etc.

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
```

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
```

## 🔁 Variants
- Compressed Trie (Radix Tree)
- Suffix Trie (for substring search)
- Ternary Search Trie (for space optimization)

---
