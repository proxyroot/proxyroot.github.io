---
title: "Trie (Prefix Tree)"
date: 2025-04-10
categories: [data-structures]
tags: [trie, prefix-tree, python]
---

# Trie (Prefix Tree)

## ✅ What It Is

A **Trie** (pronounced "try"), or **prefix tree**, is a tree-like data structure used to store a dynamic set of strings where common prefixes are shared.

Each node represents a character, and paths down the tree represent words.

## 🛠️ How to Use (Python)

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.end_of_word = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.end_of_word = True

    def search(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                return False
            node = node.children[char]
        return node.end_of_word

    def starts_with(self, prefix):
        node = self.root
        for char in prefix:
            if char not in node.children:
                return False
            node = node.children[char]
        return True
```

## 📦 Use Cases

- Autocomplete / search suggestions
- Spell checkers
- Word search and dictionary match
- IP routing / longest prefix match

## 📘 Sample Problem 1: Implement Trie

> Design a trie with insert, search, and startsWith methods.

✅ Solved above.

## 📘 Sample Problem 2: Replace Words

> Replace words in a sentence with the shortest prefix from a dictionary.

```python
def replace_words(dictionary, sentence):
    trie = Trie()
    for word in dictionary:
        trie.insert(word)

    def replace(word):
        node = trie.root
        prefix = ''
        for char in word:
            if char not in node.children:
                break
            node = node.children[char]
            prefix += char
            if node.end_of_word:
                return prefix
        return word

    return ' '.join(replace(word) for word in sentence.split())
```

## 🔁 Variants

- Suffix trie / suffix automaton
- Compressed trie (radix tree)
- Trie with count prefix/words

---

