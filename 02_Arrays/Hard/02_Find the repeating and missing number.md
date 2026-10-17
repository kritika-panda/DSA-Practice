# Find the Repeating and Missing Number ([GeeksforGeeks](https://geeksforgeeks.com) / [InterviewBit](https://interviewbit.com))

## Problem Statement

Given an unsorted array `arr` of size `n` containing positive integers. Each integer appears exactly once from `1` to `n` in the range, except for one element `A` which appears twice (repeating) and one element `B` which is missing.

Find and return **the repeating number `A` and the missing number `B`**.

Return *the answer as an array or pair containing two integers `[A, B]`*.

---

## Examples

### Example 1

```text
Input: arr = [3, 1, 3]
Output: [3, 2]
```

### Explanation

```text
The array elements are 3, 1, 3. The range from 1 to 3 should be [1, 2, 3].
- 3 appears twice, so it is the repeating element.
- 2 is omitted from the array, so it is the missing element.
```

---

### Example 2

```text
Input: arr = [4, 3, 6, 2, 1, 1]
Output: [1, 5]
```

### Explanation

```text
- 1 is the repeating element because it appears twice.
- 5 is the missing element because the range [1, 6] contains 5 but the array does not.
```

---

# Intuition

A naive approach would use a hash map or frequency array of size `n+1` to count element occurrences. This costs an \(O(N)\) extra memory penalty on the heap. Another option is sorting the array, which takes an expensive \(O(N \log N)\) time footprint.

To fulfill both strict targets—**O(N) time and O(1) space**—we can deploy a **Mathematical Summation (Algebraic)** strategy. We can compare the actual sum and sum of squares of the array elements against the expected theoretical sum and sum of squares of the natural numbers from `1` to `n`.

Let:
- \(S_n = \frac{n \times (n + 1)}{2}\) (Expected sum of numbers from 1 to n)
- \(S_{2n} = \frac{n \times (n + 1) \times (2n + 1)}{6}\) (Expected sum of squares of numbers from 1 to n)
- \(S = \sum \text{arr}[i]\) (Actual sum of array elements)
- \(S^2 = \sum \text{arr}[i]^2\) (Actual sum of squares of array elements)

We can form two simultaneous linear equations:
1. **Equation 1:** 
   \[A - B = S - S_n = X\]
2. **Equation 2:** 
   \[A^2 - B^2 = S^2 - S_{2n} = Y\]

Using the algebraic identity \(A^2 - B^2 = (A - B)(A + B)\), we can divide Equation 2 by Equation 1 to find:
\[A + B = \frac{Y}{X} = Z\]

Now we have a clean system of two linear equations:
- \(A - B = X\)
- \(A + B = Z\)

Solving for \(A\) and \(B\):
- **Repeating number (\(A\)):** \(A = \frac{X + Z}{2}\)
- **Missing number (\(B\)):** \(B = Z - A\)

---

# State Definition

To protect against integer overflows when computing sums of squares for large values of `n` (up to \(n = 10^5\)), we must compile our metrics inside 64-bit **`long`** primitive registers:

```java
long n = arr.length;
long expectedSum = (n * (n + 1)) / 2;
long expectedSqSum = (n * (n + 1) * (2 * n + 1)) / 6;
```

---

# Recurrence / Algebraic Equations Condition

```text
Calculate long actualSum = 0, actualSqSum = 0
For each num in arr:
    actualSum += num
    actualSqSum += (long)num * num

long X = actualSum - expectedSum     // Represents A - B
long Y = actualSqSum - expectedSqSum // Represents A² - B²
long Z = Y / X                       // Represents A + B

int repeating = (int)((X + Z) / 2)
int missing = (int)(Z - repeating)
Return [repeating, missing]
```

---

# Optimal Single-Pass Mathematical Solution

## Idea

We calculate the aggregate range sum constraints upfront using theoretical series formulas. We then loop through the array exactly once to accumulate the actual values and their squared counterparts on the fly. Solving the resulting linear equations isolates both elements inside a single pass.

---

## Java Code

```java
class Solution {
    public int[] findTwoElement(int[] arr) {
        // Guard Condition: Handle degenerate empty or single-item queries cleanly
        if (arr == null || arr.length < 2) {
            return new int[]{};
        }

        long n = arr.length;

        // Step 1: Compute the expected mathematical sum benchmarks for range [1, n]
        long expectedSum = (n * (n + 1)) / 2;
        long expectedSqSum = (n * (n + 1) * (2 * n + 1)) / 6;

        long actualSum = 0;
        long actualSqSum = 0;

        // Step 2: Single linear pass scan to accumulate actual values and squares
        for (int num : arr) {
            actualSum += num;
            actualSqSum += (long) num * num; // Cast to long to comfortably prevent integer overflows
        }

        // Step 3: Set up and solve the simultaneous linear algebraic system
        // X = A - B
        long X = actualSum - expectedSum;
        
        // Y = A² - B² = (A - B)(A + B)
        long Y = actualSqSum - expectedSqSum;

        // Z = A + B = Y / X
        long Z = Y / X;

        // Solve for individual entities using system elimination transformations
        int repeating = (int) ((X + Z) / 2);
        int missing = (int) (Z - repeating);

        return new int[]{repeating, missing};
    }
}
```

---

## Complexity Analysis

Let N be the total length of the input array `arr`.

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The array tracking loop processes each of the N elements exactly once. Inside the loop, it performs basic constant-time \(O(1)\) primitive addition and multiplication operations, keeping the overall execution runtime strictly linear relative to the array length N.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimal space footprint. By performing all calculations inline directly using a few local 64-bit variable registers on the heap, the algorithm bypasses the need for auxiliary lookup arrays, hash maps, or execution stacks.

---

# Key Takeaways

✅ Core fundamental paradigm for processing multiple unknown elements, stream auditing, and algebraic sequence reductions.

✅ **The 64-bit Precision Rule:** When accumulating sums of squares for large array sequences, always accumulate into 64-bit wide type configurations (`long`). For an array of size \(10^5\), the sum of squares reaches around \(\approx 3.3 \times 10^{14}\), which completely blasts past the standard signed 32-bit integer maximum cap (\(2.14 \times 10^9\)), triggering a severe integer overflow bug if not protected.

✅ Directly drives real-world microarchitectures like **network multi-packet streaming audits** (identifying if a packet transmission window suffered a duplicate insertion alongside a dropped frame payload concurrently), log integrity checkers, telemetry data pipeline validators, and memory segment auditors.

### Similar Problems

- Missing Number ([LeetCode 268](https://leetcode.com))
- Single Number ([LeetCode 136](https://leetcode.com))
- Find the Duplicate Number ([LeetCode 287](https://leetcode.com))
- First Missing Positive ([LeetCode 41](https://leetcode.com))
