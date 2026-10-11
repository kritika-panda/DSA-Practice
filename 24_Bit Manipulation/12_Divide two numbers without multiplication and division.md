# Divide Two Integers Without Multiplication and Division

## Problem Statement

Given two integers `dividend` and `divisor`, divide two integers **without using multiplication, division, and mod operator**.

Return the quotient after dividing `dividend` by `divisor`.

The integer division should truncate toward zero, which means losing its fractional part. For example, `8.345` truncates to `8` and `-2.7335` truncates to `-2`.

### Integer Overflow Boundary

Assume we are dealing with an environment that could only store integers within the **32-bit signed integer range**: \([-2^{31}, 2^{31} - 1]\). For this problem, if the quotient is **strictly greater than** 2³¹ - 1, then return 2³¹ - 1, and if the quotient is **strictly less than** -2³¹, then return -2³¹.

The overall run time complexity should be:

```text
O(log^2(n)) -> Optimized to O(log(n))
```

*(where n is the dividend value magnitude)*

---

## Examples

### Example 1

**Input**

```java
dividend = 10
divisor = 3
```

**Output**

```java
3
```

**Explanation**

`10 / 3 = 3.33333...` which truncated towards zero yields `3`.

---

### Example 2

**Input**

```java
dividend = 7
divisor = -3
```

**Output**

```java
-2
```

**Explanation**

`7 / -3 = -2.33333...` which truncated towards zero yields `-2`.

---

### Example 3

**Input**

```java
dividend = -2147483648
divisor = -1
```

**Output**

```java
2147483647
```

**Explanation**

Dividing -2³¹ by -1 results in 2³¹ (2147483648). Because 2147483648 exceeds the maximum 32-bit signed integer ceiling limit (2147483647), the clamped integer overflow value is returned.

---

## Brute Force Approach

Repeatedly subtract the absolute value of the `divisor` from the absolute value of the `dividend` while maintaining a counter list until the remainder drops below the divisor threshold.

### Steps

1. Identify the sign of the output result.
2. Convert both numbers into their absolute positive forms.
3. Keep executing `dividend = dividend - divisor` inside a loop, incrementing a `quotient` counter by 1 in each cycle.
4. Stop when `dividend < divisor`.
5. Apply the correct sign and handle the boundary overflow edge cases explicitly.

### Complexity

```text
Time Complexity: O(dividend)
Space Complexity: O(1)
```

If the dividend is very large (e.g., 2³¹) and the divisor is tiny (e.g., `1`), the subtraction loop executes billions of times, resulting in a Time Limit Exceeded (TLE) bottleneck. We can optimize this exponentially using bitwise bit shifts.

---

# Optimal Approach: Bitwise Doubling (Exponential Shifts)

## Key Idea

Instead of shifting by a constant step of `1` divisor per loop, we can speed things up exponentially by **doubling the divisor dynamically** using the bitwise **Left Shift (`<<`)** operator.

Left-shifting an integer by `1` bit multiplies its value by 2. This allows us to scale up our divisor sequence exponentially (\(1\times D, 2\times D, 4\times D, 8\times D \dots\)) to find the largest chunk that can be stripped out of the dividend in a single operation.

1. Convert inputs to a 64-bit `long` format to prevent 32-bit integer overflow negation errors during absolute value conversions.
2. Determine if the final result is negative: `isNegative = (dividend < 0) ^ (divisor < 0)`.
3. Inside a loop, find the highest power of 2 multiplier `shift` such that `(divisor << shift) <= dividend`.
4. Subtract `(divisor << shift)` from the `dividend`, and add `(1L << shift)` to our cumulative quotient tracking sum.
5. Repeat the process with the remaining dividend value until it falls strictly below the base divisor.

---

## Visual Understanding

Suppose `dividend = 10` and `divisor = 3`:

- **Iteration 1:**
  - Let's test left shifts on divisor `3`:
    - `3 << 0 = 3` (≤ 10)
    - `3 << 1 = 6` (≤ 10)
    - `3 << 2 = 12` (> 10, breaks loop)
  - The maximum safe shift is `1`. 
  - Subtract the scaled block: `dividend = 10 - 6 = 4`.
  - Accumulate quotient: `quotient += (1 << 1) = 2`.
- **Iteration 2:**
  - Test left shifts on divisor `3` against the remaining dividend `4`:
    - `3 << 0 = 3` (≤ 4)
    - `3 << 1 = 6` (> 4, breaks loop)
  - The maximum safe shift is `0`.
  - Subtract the scaled block: `dividend = 4 - 3 = 1`.
  - Accumulate quotient: `quotient += (1 << 0) = 2 + 1 = 3`.

Remaining dividend `1` is less than `3`. Loop ends. Result = `3`.

---

## Partition Variables

Let:

```java
long absDividend = Math.abs((long) dividend);
long absDivisor = Math.abs((long) divisor);
long quotient = 0;
```

---

### Border Elements

The clamping statement enforces the 32-bit signed integer bounds cleanly before returning:

```java
if (result > Integer.MAX_VALUE) return Integer.MAX_VALUE;
if (result < Integer.MIN_VALUE) return Integer.MIN_VALUE;
```

---

## Correct Partition Condition

The internal bit-shifting expansion advances forward dynamically as long as the shifted divisor segment fits within the remaining dividend threshold:

```java
int shift = 0;
while (absDividend >= (absDivisor << (shift + 1))) {
    shift++;
}
```

---

## Java Solution

```java
class Solution {

    public int divide(int dividend, int divisor) {
        // Step 1: Handle overflow edge case directly
        if (dividend == Integer.MIN_VALUE && divisor == -1) {
            return Integer.MAX_VALUE; // Clamped overflow limit
        }

        // Step 2: Determine the sign of the final result using XOR
        boolean isNegative = (dividend < 0) ^ (divisor < 0);

        // Step 3: Cast to long to handle absolute conversions near Integer.MIN_VALUE safely
        long absDividend = Math.abs((long) dividend);
        long absDivisor = Math.abs((long) divisor);
        long quotient = 0;

        // Step 4: Perform exponential bitwise alignment shifts
        while (absDividend >= absDivisor) {
            int shift = 0;
            
            // Find the highest shift power such that (divisor * 2^(shift + 1)) <= dividend
            while (absDividend >= (absDivisor << (shift + 1))) {
                shift++;
            }

            // Subtract the largest possible doubled chunk from the dividend
            absDividend -= (absDivisor << shift);
            
            // Accumulate the corresponding quotient power representation
            quotient += (1L << shift);
        }

        // Step 5: Reapply sign properties and cast back to primitive 32-bit integer limits
        long finalResult = isNegative ? -quotient : quotient;

        if (finalResult > Integer.MAX_VALUE) return Integer.MAX_VALUE;
        if (finalResult < Integer.MIN_VALUE) return Integer.MIN_VALUE;

        return (int) finalResult;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Bitwise Division Pass** | **O(log n)** | **O(1)** | The dividend drops by at least half of its size in each primary block subtraction step, bounding operations logarithmically. |

---

## Key Insight

Using bitwise left shifts to double the subtraction weight exponentially in each step transforms an O(N) linear reduction bottleneck into an efficient, \(O(\log N)\) logarithmic binary search pipeline.

```text
Time  : O(log n) performance timeline
Space : O(1) in-place variable tracking register configurations
```

---

## Similar Problems

1. Multiply Two Numbers Without Using Multiplication Operator
2. Pow(x, n) (50)
3. Sqrt(x) (69)
4. Single Number (136)
5. Sum of Two Integers (371)
