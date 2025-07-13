---
title: "Math / Number Theory Overview"
date: 2023-08-22
categories: [algorithms]
tags: [math, number-theory, python]
---

# Math / Number Theory

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

# Least Common Multiple (LCM)
def lcm(a, b):
    return a * b // gcd(a, b)

# Sieve of Eratosthenes (generate primes up to n)
def sieve(n):
    is_prime = [True] * (n+1)
    is_prime[0:2] = [False, False]
    for i in range(2, int(n**0.5) + 1):
        if is_prime[i]:
            for j in range(i*i, n+1, i):
                is_prime[j] = False
    return [i for i, prime in enumerate(is_prime) if prime]
```

## 📦 Use Cases

- Cryptography (RSA uses prime factorization)
- Counting permutations/combinations
- Modular exponentiation (e.g. power under mod)
- Math puzzles (digit sums, palindromes, trailing zeroes)

## 📘 Sample Problem 1: Count Primes

> Count the number of prime numbers less than a non-negative number `n`.

```python
def count_primes(n):
    if n < 2: return 0
    is_prime = [True] * n
    is_prime[0:2] = [False, False]
    for i in range(2, int(n**0.5) + 1):
        if is_prime[i]:
            for j in range(i*i, n, i):
                is_prime[j] = False
    return sum(is_prime)
```

## 📘 Sample Problem 2: Power of Three

> Return true if `n` is a power of three.

```python
def is_power_of_three(n):
    if n <= 0: return False
    while n % 3 == 0:
        n //= 3
    return n == 1
```

## 🔁 Variants

- Modular inverse
- Fermat’s Little Theorem
- Factorial trailing zeroes
- Counting divisors / sum of divisors

---

