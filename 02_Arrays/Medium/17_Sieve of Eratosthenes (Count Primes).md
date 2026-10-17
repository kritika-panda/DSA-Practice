# Count Primes / Sieve of Eratosthenes ([LeetCode 204](https://leetcode.com))

## Problem Statement

Given an integer `n`, return *the number of prime numbers that are strictly less than `n`*.

A **prime number** is a natural number strictly greater than 1 that has no positive divisors other than 1 and itself.

---

## Examples

### Example 1

```text
Input: n = 10
Output: 4
```

### Explanation

```text
There are 4 prime numbers less than 10, which are 2, 3, 5, and 7.
```

---

### Example 2

```text
Input: n = 0
Output: 0
```

---

### Example 3

```text
Input: n = 1
Output: 0
```

---

# Intuition

A naive brute-force approach would check every individual number from 2 to n-1 for primality by attempting to divide it by all smaller integers up to its square root. This results in an inefficient \(O(N\sqrt{N})\) runtime penalty, which will cause a Time Limit Exceeded (TLE) error for large values of n (up to 5 × 10⁶).

To optimize the search down to an ultra-fast **highly sub-linear \(O(N \log \log N)\) time footprint**, we deploy the ancient **Sieve of Eratosthenes**:
- Instead of searching for primes actively, we turn the problem inside out by **iteratively marking out non-primes (composites)**.
- We maintain a boolean lookup array `isPrime` of size `n` initialized to `true`.
- We loop starting from 2 up to \(\sqrt{n}\):
  - If `isPrime[i]` is still `true`, it means i has survived all previous composite markings. It is guaranteed to be a prime!
  - We then immediately mark all of its cross-multiplication multiples starting from **i × i** up to n as `false` (composite). 
- **The i × i Optimization Invariant:** For a prime i, any smaller multiple like \(2i, 3i, \dots, (i-1)i\) has already been visited and marked out by smaller prime factors prior to reaching this layer. Starting the marking loop straight at i² eliminates redundant overwrites.
- Once the marking phase finishes, we loop through our boolean tracking array from 2 to n-1 to count the total remaining `true` entries.

---

# State Definition

The boolean array lookup tracks prime sieve candidate markers using a flat register matrix:

```java
boolean[] isPrime = new boolean[n]; // Map caching candidate state values natively on the heap
```

---

# Recurrence / Structural Sieve Invariant

```text
Set isPrime[] = true for index >= 2
For i from 2 to sqrt(n) - 1:
    If isPrime[i] == true:
        For j from i * i to n - 1 step i:
            isPrime[j] = false
Return Count(isPrime[x] == true for 2 <= x < n)
```

---

# Optimal Space-Sieve Dynamic Marking Solution

## Idea

We allocate a single primitive `boolean[n]` array cache workspace. By combining an outer bounding loop capped at \(\sqrt{n}\) with a nested leapfrogging stride loop that filters composite multiples, we find the prime count efficiently while handling large limits easily.

---

## Java Code

```java
import java.util.Arrays;

class Solution {
    public int countPrimes(int n) {
        // Guard Condition: Numbers equal to or below 2 contain exactly zero primes strictly less than themselves
        if (n <= 2) {
            return 0;
        }

        // isPrime[i] tracks whether the integer index i is a prime number
        boolean[] isPrime = new boolean[n];
        
        // Fast block array initialization to assume all items are candidate primes initially
        Arrays.fill(isPrime, true);
        
        // Indices 0 and 1 are explicitly not prime numbers by definition
        isPrime[0] = false;
        isPrime[1] = false;

        // Phase 1: Run the Sieve of Eratosthenes marking pass
        // The outer loop condition checks i * i < n to implement the sqrt(n) ceiling limit
        for (int i = 2; i * i < n; i++) {
            // If the element has survived previous filters, it is a verified prime number
            if (isPrime[i]) {
                // Mark all higher multiple composite configurations to false
                // Optimization Step: Start marking from i * i to completely avoid redundant re-scans
                for (int j = i * i; j < n; j += i) {
                    isPrime[j] = false;
                }
            }
        }

        // Phase 2: Traverse the compiled sieve array to count the remaining prime flags
        int primeCounter = 0;
        for (int i = 2; i < n; i++) {
            if (isPrime[i]) {
                primeCounter++;
            }
        }

        return primeCounter; // Return the absolute count of primes strictly less than n
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N log log N)
```
*Explanation:* The mathematical harmonic series summation of the prime reciprocals (\(\frac{1}{2} + \frac{1}{3} + \frac{1}{5} + \dots\)) scales asymptotically bounded securely to a sub-linear \(O(N \log \log N)\) operations track. This runs almost linearly, processing millions of index slots within milliseconds.

---

### Space Complexity

```text
Space Complexity: O(N)
```
*Explanation:* Memory overhead scales linearly at O(N) on the heap to allocate the `boolean[] isPrime` tracking sieve matrix. 

*(Note: To compress the memory footprint by up to 8x for extreme system constraints, you can convert the boolean array into a bit-packed `BitSet` register layout).*

---

# Key Takeaways

✅ Core fundamental paradigm for processing numerical factorization, prime distributions, and sieve reduction systems.

✅ **The Inner Loop Short-Circuit Rule:** Starting the inner multiple scan loop right at `j = i * i` is a highly effective optimization choice. It prevents the code from executing redundant overwrites on composite numbers like 6 (which was already marked out by prime factor 2 long before prime factor 3 was even reached).

✅ Directly drives real-world microarchitectures like **asymmetric public-key cryptography encoders** (e.g., generating massive prime seeds for RSA or ECC coordinate spaces), hash distribution dividers, pseudorandom number generators, and network packet stream segment filtering.

### Similar Problems

- Ugly Number ([LeetCode 263](https://leetcode.com))
- Prime Arrangements ([LeetCode 1175](https://leetcode.com))
- Distinct Prime Factors of Product of Array ([LeetCode 2521](https://leetcode.com))
- Closest Prime Numbers in Range ([LeetCode 2523](https://leetcode.com))
