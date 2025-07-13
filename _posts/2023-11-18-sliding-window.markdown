---
title: "Sliding Window Technique"
date: 2023-11-18
categories: [algorithms]
tags: [sliding-window, python]
---

The Sliding Window technique efficiently solves problems involving contiguous subarrays or substrings by maintaining a window that moves through the data. This post covers sliding window patterns, their use cases, and provides practical Python examples and problems for mastering this approach.

# Sliding Window

## ✅ What It Is

**Sliding Window** is a technique to efficiently solve problems that involve **contiguous subarrays or substrings**.

- Use two pointers (`left`, `right`) to represent a window
- Move the window to adjust to conditions (e.g. size, sum, frequency)
- Can be fixed-size or dynamic-size

## 🛠️ How to Use (Python)

```python
# Example: Max sum of subarray of size k using sliding window
def max_sum_subarray(nums, k):
    max_sum = cur_sum = sum(nums[:k])  # Sum of first window
    for i in range(k, len(nums)):
        cur_sum += nums[i] - nums[i - k]  # Slide window: add new, remove old
        max_sum = max(max_sum, cur_sum)   # Update max if needed
    return max_sum
# Time complexity: O(n), Space complexity: O(1)
```

## 📦 Use Cases

- Substrings/subarrays
- Longest substring with/without constraints
- K distinct elements
- Sliding window max/min

## 📘 Sample Problem 1: Longest Substring Without Repeating Characters

> Return length of the longest substring without repeating characters.

```python
# This function finds the length of the longest substring without repeating characters.
def length_of_longest_substring(s):
    seen = {}  # Dictionary to store last seen index of each character
    left = max_len = 0
    for right, char in enumerate(s):
        if char in seen and seen[char] >= left:
            left = seen[char] + 1  # Move left pointer after last occurrence
        seen[char] = right         # Update last seen index
        max_len = max(max_len, right - left + 1)
    return max_len
# Time complexity: O(n), Space complexity: O(min(n, m)), m = charset size
```

## 📘 Sample Problem 2: Minimum Window Substring

> Find the smallest substring of `s` that contains all characters of `t`.

```python
from collections import Counter

# This function finds the minimum window substring containing all characters of t.
def min_window(s, t):
    if not s or not t:
        return ""
    t_count = Counter(t)  # Count of chars in t
    window = {}
    have, need = 0, len(t_count)
    res = [-1, -1]
    res_len = float("inf")
    left = 0

    for right, c in enumerate(s):
        window[c] = window.get(c, 0) + 1
        if c in t_count and window[c] == t_count[c]:
            have += 1
        while have == need:
            if (right - left + 1) < res_len:
                res = [left, right]
                res_len = right - left + 1
            window[s[left]] -= 1
            if s[left] in t_count and window[s[left]] < t_count[s[left]]:
                have -= 1
            left += 1

    l, r = res
    return s[l:r+1] if res_len != float("inf") else ""
# Time complexity: O(n), Space complexity: O(m), m = charset size
```

## 🔁 Variants

- Sliding window max (use deque)
- Fixed-length vs. shrinking window
- Frequency maps and counting within window

---

