---
title: "Math / Number Theory Overview"
date: 2023-08-22
categories: [algorithms]
tags: [math, number-theory, python]
---

Math and Number Theory in algorithms involve solving problems using mathematical principles such as primes, GCD/LCM, modular arithmetic, and combinatorics. This post covers key techniques, use cases, and provides practical Python examples and problems for mastering math-based algorithms.

## Overview

This category involves solving problems using mathematical principles like:

- **Primes & Sieve algorithms**
- **GCD / LCM**
- **Modular arithmetic**
- **Combinatorics**
- **Number properties** (palindromes, digits, factors)

## 🧩 Visualizing Number Theory

### Sieve of Eratosthenes (n = 20)

```mermaid
graph TD
    Start[Start: All numbers marked as prime]
    Step1[Step 1: Mark multiples of 2 as non-prime]
    Step2[Step 2: Mark multiples of 3 as non-prime]
    Final[Final: Primes 2,3,5,7,11,13,17,19]
    
    Start --> Step1 --> Step2 --> Final
    
    Initial[Initial: T,T,T,T,T,T,T,T,T,T,T,T,T,T,T,T,T,T,T,T,T]
    After2[After 2: F,F,T,T,F,T,F,T,F,T,F,T,F,T,F,T,F,T,F,T,F]
    After3[After 3: F,F,T,T,F,T,F,T,F,F,F,T,F,T,F,F,F,T,F,T,F]
    
    Start -.-> Initial
    Step1 -.-> After2
    Step2 -.-> After3
    Final -.-> Primes[Primes: 2,3,5,7,11,13,17,19]
    
    style Final fill:#99ff99
    style Primes fill:#99ccff
```

---

## 🛠️ Common Techniques

```python
# Greatest Common Divisor (GCD)
from math import gcd

gcd(12, 18)  # returns 6
# Time complexity: O(log(min(a, b)))

# Least Common Multiple (LCM)
def lcm(a, b):
    return a * b // gcd(a, b)  # LCM formula using GCD
# Time complexity: O(log(min(a, b)))

# Sieve of Eratosthenes (generate primes up to n)
def sieve(n):
    is_prime = [True] * (n+1)
    is_prime[0:2] = [False, False]  # 0 and 1 are not prime
    for i in range(2, int(n**0.5) + 1):
        if is_prime[i]:
            for j in range(i*i, n+1, i):
                is_prime[j] = False  # Mark multiples as not prime
    return [i for i, prime in enumerate(is_prime) if prime]
# Time complexity: O(n log log n), Space: O(n)

# Example:
print(gcd(12, 18))  # Output: 6
print(lcm(12, 18))  # Output: 36
print(sieve(20))    # Output: [2, 3, 5, 7, 11, 13, 17, 19]
```

---

## 🧩 GCD/LCM Step-by-Step

```mermaid
graph TD
    Start[Start: a=12, b=18]
    Step1[Step 1: 12 % 18 = 12\na=18, b=12]
    Step2[Step 2: 18 % 12 = 6\na=12, b=6]
    Step3[Step 3: 12 % 6 = 0\na=6, b=0]
    GCD[GCD = 6]
    LCM[LCM = 12 × 18 ÷ 6 = 36]
    Result[Result: GCD=6, LCM=36]

    Start --> Step1 --> Step2 --> Step3 --> GCD --> LCM --> Result
    style Result fill:#99ff99
```

### GCD of 12 and 18 using Euclidean Algorithm

| Step | a  | b  | a % b | New a | New b |
|------|----|----|-------|-------|-------|
| 1    | 12 | 18 | 12    | 18    | 12    |
| 2    | 18 | 12 | 6     | 12    | 6     |
| 3    | 12 | 6  | 0     | 6     | 0     |

- GCD(12, 18) = 6
- LCM(12, 18) = (12 × 18) ÷ 6 = 36

---

## 🧩 Count Primes Visualization

For n = 10, the sieve process:

```mermaid
graph TD
    Start[Start: n = 10]
    Step1[Step 1: Mark 0,1 as non-prime]
    Step2[Step 2: Mark multiples of 2 as non-prime]
    Step3[Step 3: Mark multiples of 3 as non-prime]
    Final[Final: Count primes]
    
    Start --> Step1 --> Step2 --> Step3 --> Final
    
    Initial[Initial: F,F,T,T,T,T,T,T,T,T]
    After2[After 2: F,F,T,T,F,T,F,T,F,T]
    After3[After 3: F,F,T,T,F,T,F,T,F,F]
    Result[Result: 4 primes 2,3,5,7]
    
    Step1 -.-> Initial
    Step2 -.-> After2
    Step3 -.-> After3
    Final -.-> Result
    
    style Final fill:#99ff99
    style Result fill:#99ccff
```

---

## 📘 Sample Problem 1: Count Primes

> Count the number of prime numbers less than a non-negative number `n`.

```python
# This function counts the number of primes less than n using the Sieve of Eratosthenes.
def count_primes(n):
    if n < 2: return 0
    is_prime = [True] * n
    is_prime[0:2] = [False, False]  # 0 and 1 are not prime
    for i in range(2, int(n**0.5) + 1):
        if is_prime[i]:
            for j in range(i*i, n, i):
                is_prime[j] = False  # Mark multiples as not prime
    return sum(is_prime)
# Time complexity: O(n log log n), Space: O(n)

# Example:
n = 10
print(count_primes(n))  # Output: 4 (primes: 2, 3, 5, 7)
```

---

## 🧩 Power of Three Flow

```mermaid
graph TD
    Start["Start: n = 27"]
    Step1["Step 1: 27 % 3 = 0\n27 ÷ 3 = 9"]
    Step2["Step 2: 9 % 3 = 0\n9 ÷ 3 = 3"]
    Step3["Step 3: 3 % 3 = 0\n3 ÷ 3 = 1"]
    Step4["Step 4: 1 % 3 = 1\nn = 1"]
    Check["Check: n == 1?"]
    Result["Result: True (27 is power of 3)"]

    Start --> Step1 --> Step2 --> Step3 --> Step4 --> Check --> Result
    style Result fill:#99ff99
```

Suppose n = 27

| Step | n  | n % 3 | n // 3 | Action        |
|------|----|-------|--------|---------------|
| 1    | 27 | 0     | 9      | Divide by 3   |
| 2    | 9  | 0     | 3      | Divide by 3   |
| 3    | 3  | 0     | 1      | Divide by 3   |
| 4    | 1  | 1     | -      | Check if 1    |

- Since n becomes 1, 27 is a power of 3 (3³ = 27).

---

## 📘 Sample Problem 2: Power of Three

> Return true if `n` is a power of three.

```python
# This function checks if n is a power of three.
def is_power_of_three(n):
    if n <= 0: return False
    while n % 3 == 0:
        n //= 3  # Keep dividing by 3
    return n == 1  # True if reduced to 1
# Time complexity: O(log_3 n), Space: O(1)

# Example:
print(is_power_of_three(27))  # Output: True (3³ = 27)
print(is_power_of_three(10))  # Output: False
```