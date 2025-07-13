---
title: "Trie (Prefix Tree) Overview"
date: 2024-02-14
categories: [data-structures]
tags: [trie, prefix-tree, python]
---

A Trie (prefix tree) is a tree-like data structure optimized for storing and searching strings, especially for prefix-based operations. This post covers the basics of Tries, their use cases, and provides practical Python examples and problems for string manipulation and search.

## ✅ What It Is
A **Trie** is a tree-like data structure used to efficiently store and retrieve **strings**, especially useful for **prefix-based operations**.

- Each node represents a character
- Paths down the tree represent words
- Efficient for autocomplete, dictionary matching, etc.

## 🛠️ How to Use (Python)
```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end = True

    def search(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                return False
            node = node.children[char]
        return node.is_end

    def starts_with(self, prefix):
        node = self.root
        for char in prefix:
            if char not in node.children:
                return False
            node = node.children[char]
        return True
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
