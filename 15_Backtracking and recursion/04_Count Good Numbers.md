# Count Good Numbers ([LeetCode 1922](https://leetcode.com))

## Problem Statement

A 0-indexed string of digits is called **good** if the digits at **even indices** are **even** (`0, 2, 4, 6, 8`) and the digits at **odd indices** are **prime** (`2, 3, 5, 7`).

- For example, `"2582"` is good because the digits at even indices (0 and 2) are `2` and `8` (both even), and the digits at odd indices (1 and 3) are `5` and `2` (both prime).
- On the other hand, `"3245"` is not good because index 0 contains `3` which is not even.

Given an integer `n` representing the length of a digit string, return *the total number of **good** digit strings of length `n`*. Since the answer may be very large, return it **modulo** `10⁹ + 7`.

---

## Examples

### Example 1

```text
Input: n = 1
Output: 5
```

### Explanation

```text
A good string of length 1 has exactly 1 even index (index 0). 
The valid choices for index 0 are the 5 even digits: 0, 2, 4, 6, 8. 
Hence, there are 5 good numbers.
```

---

### Example 2

```text
Input: n = 4
Output: 400
```

### Explanation

```text
A good string of length 4 contains:
- 2 even indices (0, 2) -> each has 5 choices (even digits)
- 2 odd indices (1, 3)  -> each has 4 choices (prime digits: 2, 3, 5, 7)
Total combinations = 5 × 4 × 5 × 4 = 5² × 4² = 25 × 16 = 400.
```

---

### Example 3

```text
Input: n = 50
Output: 564908303
```

### Explanation

```text
The absolute combinatorial count is massive, so the final total is returned modulo 10⁹ + 7.
```

---

# Intuition

This problem can be mathematically reduced to a **Combinatorial Counting** problem involving large exponent powers.

Let's break down a string of length `n`:
- The number of **even indices** is \(\lceil n / 2 \rceil\), which can be written as `(n + 1) / 2`. Each of these positions has exactly **5 choices** (`0, 2, 4, 6, 8`).
- The number of **odd indices** is \(\lfloor n / 2 \rfloor\), which can be written as `n / 2`. Each of these positions has exactly **4 choices** (`2, 3, 5, 7`).

By the fundamental counting principle, the total number of good strings of length `n` is exactly:
\[\text{Total Combinations} = 5^{\text{even\_indices}} \times 4^{\text{odd\_indices}}\]

Since `n` can be as massive as \(10^{15}\), computing this using a standard linear loop will cause a Time Limit Exceeded (TLE) error. To compute large exponents in logarithmic time, we must combine **Binary Exponentiation (Modular Exponentiation)** with regular modulo reductions (`MOD = 10⁹ + 7`) at each step.

---

# Brute Force Solution

## Idea

We figure out the counts for the odd and even indices. We then launch a simple linear loop to multiply our running total by `5` for each even position and by `4` for each odd position, reducing the final total modulo `10⁹ + 7` during every multiplication step. This hits a timeout boundary when `n` scales upward.

---

## Java Code

```java
class Solution {
    public int countGoodNumbers(long n) {
        long MOD = 1_000_000_007;
        long evenIndices = (n + 1) / 2;
        long oddIndices = n / 2;
        
        long result = 1;
        
        // Linear multiplication pass for even indices
        for (long i = 0; i < evenIndices; i++) {
            result = (result * 5) % MOD;
        }
        
        // Linear multiplication pass for odd indices
        for (long i = 0; i < oddIndices; i++) {
            result = (result * 4) % MOD;
        }
        
        return (int) result;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the code maps out separate sequential multiplication loops running up to `n` times overall.
- **Space Complexity:** O(1) constant auxiliary space footprint.

---

# State Definition (Optimal Approach)

To formalise our calculations inside a fast logarithmic track, we define our states using two independent modular exponentiation subproblems:

```text
evenPower = power(5, (n + 1) / 2) % MOD
oddPower = power(4, n / 2) % MOD
return (evenPower * oddPower) % MOD
```

---

# Recurrence / Structural Modular Power Condition

```text
power(base, exp):
    If exp == 0 -> Return 1
    
    half = power(base, exp / 2)
    halfProduct = (half * half) % MOD
    
    If exp % 2 == 0 -> Return halfProduct
    Else            -> Return (base * halfProduct) % MOD
```

---

# Optimal Modular Binary Exponentiation Solution

## Idea

We isolate the structural index counts for our digit fields. We then compute \(5^{\text{even}}\) and \(4^{\text{odd}}\) independently using **Binary Exponentiation by Squaring** in an iterative or recursive format. Squaring the base and halving the exponent power at each step cuts our operational tracking depth down to an efficient \(O(\log N)\) logarithmic scale.

---

## Java Code

```java
class Solution {
    private static final long MOD = 1_000_000_007;

    public int countGoodNumbers(long n) {
        // Compute total slots assigned to even indices: ceil(n / 2)
        long evenIndices = (n + 1) / 2;
        // Compute total slots assigned to odd indices: floor(n / 2)
        long oddIndices = n / 2;

        // Compute 5^evenIndices % MOD
        long totalEvenCombinations = modularPower(5, evenIndices);
        // Compute 4^oddIndices % MOD
        long totalOddCombinations = modularPower(4, oddIndices);

        // Combine the independent subsets via the fundamental counting principle
        return (int) ((totalEvenCombinations * totalOddCombinations) % MOD);
    }

    // High-speed O(log exp) Modular Binary Exponentiation engine
    private long modularPower(long base, long exp) {
        long res = 1;
        base = base % MOD;

        while (exp > 0) {
            // Parity Check: If the current power exponent is odd
            if (exp % 2 != 0) {
                res = (res * base) % MOD; // Absorb base factor into result accumulator
            }
            
            // Square the base to match the halved exponent power frame
            base = (base * base) % MOD;
            
            // Halve the exponent power for the subsequent iteration step
            exp = exp / 2;
        }

        return res;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(log n)
```
*Explanation:* The modular exponentiation method divides the exponent variables `evenIndices` and `oddIndices` by 2 during every single cycle step. This guarantees that total loop executions scale strictly logarithmically relative to string length `n`, processing huge numeric profiles seamlessly.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. The algorithm calculates combinations iteratively using inline primitive scalar variables, completely bypassing the stack frame memory overhead of recursion or tracking helper tables.

---

# Key Takeaways

✅ Advanced combinatorial problem combining elementary mathematical rules with logarithmic power reductions.

✅ **The Modulo Arithmetic Overflow Rule:** When performing multiplication actions inside modular tracking loops (`res * base`), always convert variables to `long` primitive data formats beforehand. Even if individual factors fit neatly into 32-bit limits, their temporary un-reduced multiplication products can overflow past maximum integer values before the modulo operator has a chance to execute.

✅ Directly drives real-world architectures like **asymmetric cryptography block scramblers** (e.g., modular power calculations inside Diffie-Hellman or RSA encryptions), combinatorial security validators, token generation engines, and sequence format checkers.

✅ Common Architectural Paradigm:

```text
Logarithmic Modular Power Squaring for Massive Combinatorial Stream Extractions
```

### Similar Problems

- Pow(x, n) ([LeetCode 50](https://leetcode.com))
- Super Pow ([LeetCode 372](https://leetcode.com))
- Combinations ([LeetCode 77](https://leetcode.com))
- Fibonacci Number ([LeetCode 509](https://leetcode.com))
