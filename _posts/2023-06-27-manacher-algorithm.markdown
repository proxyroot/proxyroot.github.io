---
title: "Manacher's Algorithm Overview"
date: 2023-06-27
categories: [algorithms]
tags: [palindrome, manacher, strings, dp]
---

Manacher's Algorithm is a brilliant linear-time solution for finding the longest palindromic substring in a string. By transforming the input and using symmetry, it avoids the O(n^2) brute-force approach. This post explains the intuition, transformation, and implementation, with annotated code for beginners.

---

## What is Manacher's Algorithm?

Manacher's algorithm transforms the string to handle even and odd length palindromes uniformly, then uses a center-expansion approach with memory of previous expansions.

- Time complexity: **O(n)**
- Space complexity: **O(n)**

---

## Key Transformation

We insert `#` between characters (and at the ends) to avoid separately handling even-length palindromes.

```python
# Example: "abba" => "^#a#b#b#a#$"
# The ^ and $ are sentinels to avoid bounds checking.
```

---

## Example: Manacher's Algorithm in Python

```python
def manacher(s):
    """
    Manacher's Algorithm: Find the longest palindromic substring in O(n) time.
    Returns the longest palindromic substring.
    """
    # Preprocess the string with sentinels and separators
    t = '^#' + '#'.join(s) + '#$'
    n = len(t)
    p = [0] * n  # p[i]: radius of palindrome centered at i
    center = right = 0

    for i in range(1, n - 1):
        mirror = 2 * center - i  # Mirror position of i around center

        if i < right:
            # Use previously computed palindrome info
            p[i] = min(right - i, p[mirror])

        # Expand around center i
        while t[i + (1 + p[i])] == t[i - (1 + p[i])]:
            p[i] += 1

        # Update center and right boundary if expanded past right
        if i + p[i] > right:
            center, right = i, i + p[i]

    # Find maximum length and center
    max_len = max(p)
    center_index = p.index(max_len)
    # Map back to original string index
    start = (center_index - max_len) // 2
    return s[start: start + max_len]

# Example usage:
# s = "babad"
# print(manacher(s))  # Output: "bab" or "aba"
```
# Approach:
- Transform the string to handle even/odd palindromes uniformly.
- Use an array p[] to store the radius of palindrome at each center.
- Expand around each center, using previously computed info for efficiency.
- Map the result back to the original string.

---

## Use Cases

- Find longest palindromic substring
- Count palindromic substrings (variant)
- Center-expansion optimizations

---

## Variants

- Count of all palindromic substrings
- Palindromic substrings of bounded length
- Combined with dynamic programming for enhancements

---

Use Manacher’s when you need lightning-fast palindrome detection! 