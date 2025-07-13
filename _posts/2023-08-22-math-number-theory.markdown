---
title: "Math / Number Theory Overview"
date: 2023-08-22
categories: [algorithms]
tags: [math, number-theory, python]
---

Math and Number Theory in algorithms involve solving problems using mathematical principles such as primes, GCD/LCM, modular arithmetic, and combinatorics. This post covers key techniques, use cases, and provides practical Python examples and problems for mastering math-based algorithms.

## ✅ What It Is

This category involves solving problems using mathematical principles like:

- **Primes & Sieve algorithms**
- **GCD / LCM**
- **Modular arithmetic**
- **Combinatorics**
- **Number properties** (palindromes, digits, factors)

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
```

## 📦 Use Cases

- Cryptography (RSA uses prime factorization)
- Counting permutations/combinations
- Modular exponentiation (e.g. power under mod)
- Math puzzles (digit sums, palindromes, trailing zeroes)

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
```

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
```

## 🔁 Variants

- Modular inverse
- Fermat’s Little Theorem
- Factorial trailing zeroes
- Counting divisors / sum of divisors

---

