---
title: "Sliding Window Technique"
date: 2023-11-18
categories: [algorithms]
tags: [sliding-window, window, two-pointers, algorithms, python, coding-interview, leetcode, tutorial, guide, programming, substring, subarray, frequency-map, max-min, problem-solving, big-o, time-complexity, optimization, dynamic-window, fixed-window]
---

The Sliding Window technique efficiently solves problems involving contiguous subarrays or substrings by maintaining a window that moves through the data. This post covers sliding window patterns, their use cases, and provides practical Python examples and problems for mastering this approach.

# Sliding Window

## Overview

**Sliding Window** is a technique to efficiently solve problems that involve **contiguous subarrays or substrings**.

- Use two pointers (`left`, `right`) to represent a window
- Move the window to adjust to conditions (e.g. size, sum, frequency)
- Can be fixed-size or dynamic-size

---

## 🧩 Visualizing Sliding Window

### Fixed-Size Window (k = 3)

```
Array: [1, 4, 2, 10, 2, 3, 1, 0, 20]

Window 1: [1, 4, 2]     → Sum: 7
Window 2: [4, 2, 10]    → Sum: 16
Window 3: [2, 10, 2]    → Sum: 14
Window 4: [10, 2, 3]    → Sum: 15
Window 5: [2, 3, 1]     → Sum: 6
Window 6: [3, 1, 0]     → Sum: 4
Window 7: [1, 0, 20]    → Sum: 21

Max sum: 21
```

### Dynamic Window

```
String: "abcabcbb"
Window: [a] → [ab] → [abc] → [bca] → [cab] → [abc] → [cb] → [b]
Lengths:  1     2      3      3      3      3      2     1
Max length: 3
```

---

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

# Example:
nums = [1, 4, 2, 10, 2, 3, 1, 0, 20]
k = 3
print(max_sum_subarray(nums, k))  # Output: 21
```

---

## 🧩 Longest Substring Without Repeating Characters Flow

```mermaid
graph TD
    Start[Start: s = "abcabcbb"]
    Step1[Step 1: window="a", max_len=1]
    Step2[Step 2: window="ab", max_len=2]
    Step3[Step 3: window="abc", max_len=3]
    Step4[Step 4: window="bca", max_len=3]
    Step5[Step 5: window="cab", max_len=3]
    Step6[Step 6: window="abc", max_len=3]
    Step7[Step 7: window="cb", max_len=3]
    Step8[Step 8: window="b", max_len=3]
    Result[Result: 3 (longest substring)]

    Start --> Step1 --> Step2 --> Step3 --> Step4 --> Step5 --> Step6 --> Step7 --> Step8 --> Result
    style Result fill:#99ff99
```

## 🧩 Longest Substring Without Repeating Characters Step-by-Step

Suppose s = "abcabcbb"

| right | char | char in seen? | seen[char] >= left? | left | window | max_len |
|-------|----|---------------|---------------------|------|--------|---------|
| 0     | a  | No            | -                   | 0    | [a]    | 1       |
| 1     | b  | No            | -                   | 0    | [ab]   | 2       |
| 2     | c  | No            | -                   | 0    | [abc]  | 3       |
| 3     | a  | Yes           | 0 >= 0              | 1    | [bca]  | 3       |
| 4     | b  | Yes           | 1 >= 1              | 2    | [cab]  | 3       |
| 5     | c  | Yes           | 2 >= 2              | 3    | [abc]  | 3       |
| 6     | b  | Yes           | 4 >= 3              | 5    | [cb]   | 3       |
| 7     | b  | Yes           | 6 >= 5              | 7    | [b]    | 3       |

- Final result: 3 (substring "abc").

---

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

# Example:
s = "abcabcbb"
print(length_of_longest_substring(s))  # Output: 3
```

---

## 🧩 Minimum Window Substring Flow

Suppose s = "ADOBECODEBANC", t = "ABC"

| right | char | window | have | need | have == need? | left | window_size | min_window |
|-------|----|--------|------|------|---------------|------|-------------|------------|
| 0     | A  | {A:1}  | 1    | 3    | No            | 0    | 1           | -          |
| 1     | D  | {A:1,D:1} | 1 | 3    | No            | 0    | 2           | -          |
| 2     | O  | {A:1,D:1,O:1} | 1 | 3 | No            | 0    | 3           | -          |
| 3     | B  | {A:1,D:1,O:1,B:1} | 2 | 3 | No            | 0    | 4           | -          |
| 4     | E  | {A:1,D:1,O:1,B:1,E:1} | 2 | 3 | No            | 0    | 5           | -          |
| 5     | C  | {A:1,D:1,O:1,B:1,E:1,C:1} | 3 | 3 | Yes           | 0    | 6           | "ADOBEC"   |
| 5     | C  | {A:0,D:1,O:1,B:1,E:1,C:1} | 2 | 3 | No            | 1    | 5           | "ADOBEC"   |
| ...   | ... | ...    | ...  | ...  | ...           | ...  | ...         | ...        |

- Final result: "BANC" (minimum window containing all characters of t).

---

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

# Example:
s = "ADOBECODEBANC"
t = "ABC"
print(min_window(s, t))  # Output: "BANC"
```

---

## 🔁 Variants

- Sliding window max (use deque)
- Fixed-length vs. shrinking window
- Frequency maps and counting within window

---

