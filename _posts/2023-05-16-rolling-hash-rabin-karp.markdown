---
title: "Rolling Hash & Rabin-Karp Explained"
date: 2023-05-16
categories: [algorithms]
tags: [strings, hashing, rabin-karp, rolling-hash]
---

The Rabin-Karp algorithm is a classic example of rolling hash applied to substring matching. Instead of comparing characters one by one, it compares hash values to identify potential matches, making substring search efficient. This post explains the rolling hash technique, Rabin-Karp implementation, and practical use cases, with annotated code for beginners.

---

## What is Rolling Hash & Rabin-Karp?

- A **rolling hash** lets you efficiently compute hash values for all substrings of fixed length in linear time.
- Rabin-Karp uses this for string matching, especially when multiple patterns are involved.

---

## Hashing Technique

Given a base `b` and a modulus `m`, compute the hash for string `s[0..k-1]` as:

```python
# Compute hash for s[0..k-1]:
# H = (s[0]*b^(k-1) + s[1]*b^(k-2) + ... + s[k-1]*b^0) % m
# To slide the window:
# new_H = (old_H - s[i]*b^(k-1))*b + s[i+k] % m
```

This saves recomputing the hash from scratch each time.

---

## Example: Rabin-Karp in Python

```python
def rabin_karp(text, pattern, base=256, mod=10**9+7):
    """
    Rabin-Karp algorithm for substring search.
    Returns the starting index of the first occurrence of pattern in text, or -1 if not found.
    Time Complexity: O(n), Space Complexity: O(1)
    """
    n, m = len(text), len(pattern)
    if m > n:
        return -1

    # Precompute base^(m-1) % mod for rolling
    high_base = pow(base, m - 1, mod)
    t_hash = p_hash = 0

    # Initial hash values for pattern and first window
    for i in range(m):
        p_hash = (p_hash * base + ord(pattern[i])) % mod
        t_hash = (t_hash * base + ord(text[i])) % mod

    for i in range(n - m + 1):
        # If hash matches, confirm by direct substring comparison
        if p_hash == t_hash and text[i:i + m] == pattern:
            return i

        # Update hash: remove leading char, add next char
        if i < n - m:
            t_hash = (t_hash - ord(text[i]) * high_base) % mod
            t_hash = (t_hash * base + ord(text[i + m])) % mod
            t_hash = (t_hash + mod) % mod  # Avoid negative hash

    return -1  # Pattern not found

# Example usage:
# text = "abcxabcdabcdabcy"
# pattern = "abcdabcy"
# print(rabin_karp(text, pattern))  # Output: 8
```
# Approach:
# - Compute hash for the pattern and the first window of text.
# - Slide the window: update the hash in O(1) time.
# - If hashes match, check the substring directly to avoid false positives.
# - Handles large texts efficiently, especially with multiple patterns.

---

## Use Cases

- Substring search with large texts
- Plagiarism detection
- Detecting duplicate strings
- DNA sequence analysis

---

## Variants

- Multiple pattern matching
- Double hashing (reduce collisions)
- 2D Rabin-Karp for grid/string pattern matching

---

Rabin-Karp is simple and fast — especially when used with hash tricks like double hashing to reduce false positives. 