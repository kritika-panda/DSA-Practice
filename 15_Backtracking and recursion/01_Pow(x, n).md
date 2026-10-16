# Pow(x, n) ([LeetCode 50](https://leetcode.com))

## Problem Statement

Implement `pow(x, n)`, which calculates `x` raised to the power `n` (i.e., \(x^n\)).

---

## Examples

### Example 1

```text
Input: x = 2.00000, n = 10
Output: 1024.00000
```

---

### Example 2

```text
Input: x = 2.10000, n = 3
Output: 9.26100
```

---

### Example 3

```text
Input: x = 2.00000, n = -2
Output: 0.25000
```

### Explanation

```text
2⁻² = 1 / (2²) = 1 / 4 = 0.25
```

---

# Intuition

A naive approach would multiply `x` by itself `n` times using a simple loop. However, this takes an inefficient O(N) linear time footprint, which will cause a Time Limit Exceeded (TLE) error when `n` is large (e.g., n = 2³¹ - 1).

To optimize the execution down to an ultra-fast **\(O(\log N)\) logarithmic time**, we can use the **Exponentiation by Squaring** algorithm (Binary Exponentiation / Divide and Conquer):
- Instead of computing \(x^n\) directly, we can notice that:
  - If `n` is **even**: \(x^n = (x^2)^{n/2}\) (e.g., 2¹⁰ = 4⁵)
  - If `n` is **odd**: \(x^n = x \times x^{n-1} = x \times (x^2)^{(n-1)/2}\) (e.g., 2⁹ = 2 × 4⁴)
- By squaring the base `x` and halving the power `n` at each step, we can cut the number of required multiplications in half during every single iteration.
- **Handling Negative Exponents:** If `n` is negative, \(x^n\) is equivalent to \((1/x)^{-n}\). We can convert `x` to `1/x` and flip `n` to its absolute positive counterpart. 
- **The Integer Overflow Guard:** In Java, the minimum possible value for a 32-bit signed integer is `-2147483648` (`Integer.MIN_VALUE`). If we attempt to flip its sign by doing `-n`, it will overflow past the maximum positive limit (`2147483647`). To safeguard against this, we convert `n` to a 64-bit `long` primitive data type before processing.

---

# Brute Force Solution

## Idea

We check if `n` is negative, apply the reciprocal to `x`, and convert `n` to a positive count. We then use a straight forward `for` loop to multiply `x` by itself `n` times sequentially.

---

## Java Code

```java
class Solution {
    public double myPow(double x, int n) {
        if (n == 0) return 1.0;
        
        long N = n;
        if (N < 0) {
            x = 1 / x;
            N = -N;
        }
        
        double result = 1.0;
        // Linear multiplication loop
        for (long i = 0; i < N; i++) {
            result *= x;
        }
        
        return result;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the multiplication loop runs exactly `n` times.
- **Space Complexity:** O(1) absolute constant auxiliary space footprint.

---

# State Definition (Optimal Approach)

To formalise our calculations inside a fast binary track, we track the calculation using two local mathematical registers:

```java
long N = n;       // 64-bit integer to safely prevent Integer.MIN_VALUE overflow bugs
double ans = 1.0; // Running product accumulator register storing the results
```

---

# Recurrence / Structural State Condition

```text
While N > 0:
    If N is odd  -> ans = ans * x
    x = x * x    // Square the base
    N = N / 2    // Halve the exponent
```

---

# Optimal Iterative Binary Exponentiation Solution

## Idea

We cast `n` safely to a `long` variable and adjust for negative values. Inside an iterative loop, we inspect if the current exponent power is odd by running a bitwise modulo check (`N % 2 != 0`). If it matches, we pull out one factor of `x` into our `ans` accumulator. We then square the base `x = x * x` and halve the power `N = N / 2` during every cycle step, keeping memory allocations flat.

---

## Java Code

```java
class Solution {
    public double myPow(double x, int n) {
        // Base Case: Any non-zero base raised to the power of 0 yields exactly 1.0
        if (n == 0) {
            return 1.0;
        }

        // Use a 64-bit long to safely protect against Integer.MIN_VALUE overflow cascades
        long N = n;

        // If the exponent parameter is negative, convert the expression to its reciprocal
        if (N < 0) {
            x = 1 / x;
            N = -N;
        }

        double ans = 1.0;
        double currentProduct = x;

        // Core Step: Iterative Exponentiation by Squaring (Binary Exponentiation)
        while (N > 0) {
            // Parity Check: If the current power exponent is odd
            if (N % 2 != 0) {
                ans = ans * currentProduct; // Absorb one factor into our result accumulator
            }
            
            // Square the base to match the halved exponent power frame
            currentProduct = currentProduct * currentProduct;
            
            // Halve the exponent power for the subsequent iteration step
            N = N / 2;
        }

        return ans;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(log n)
```
*Explanation:* The value of our exponent register `N` is divided by 2 during every loop step. This causes the total execution loop runtime to scale strictly logarithmically relative to the exponent magnitude, processing massive numbers instantly.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal. The algorithm computes the values iteratively using inline scalar registers, completely bypassing the stack frame overhead of recursion or array helper buffers.

---

# Key Takeaways

✅ Core fundamental numerical computing pattern for high-speed logarithmic power evaluation.

✅ **The Modulo Sign Overflow Rule:** Always cast boundary integers to a wider type allocation format (`long N = n`) right away if your algorithm relies on negating values (`-n`). Failing to account for `-2147483648` will break your code because its absolute value cannot be mapped onto standard signed 32-bit limits.

✅ Directly drives real-world architectures like **RSA asymmetric cryptography key generators** (which require modular exponentiation with massive bit lengths), graphic shading engines, calculation matrices, and physics simulators.

✅ Common Architectural Paradigm:

```text
Bitwise Exponentiation by Squaring for Logarithmic Scale Power Reductions
```

### Similar Problems

- Sqrt(x) ([LeetCode 69](https://leetcode.com))
- Super Pow ([LeetCode 372](https://leetcode.com))
- Count Good Numbers ([LeetCode 1922](https://leetcode.com))
- Fibonacci Number ([LeetCode 509](https://leetcode.com))
