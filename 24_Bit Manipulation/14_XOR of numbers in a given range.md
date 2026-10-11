# XOR of Numbers in a Given Range

## Problem Statement

Given two integers `L` and `R`, find the cumulative bitwise **XOR of all numbers in the range [L, R]** inclusive. That is, calculate:

```text
L ^ (L + 1) ^ (L + 2) ^ ... ^ R
```

The overall runtime complexity must be strictly bounded by:

```text
O(1)
```

---

## Examples

### Example 1

**Input**

```java
L = 4, R = 8
```

**Output**

```java
8
```

**Explanation**

The range is `[4, 5, 6, 7, 8]`. The cumulative XOR is calculated as:
`4 ^ 5 ^ 6 ^ 7 ^ 8`
- `4 ^ 5 = 1`
- `1 ^ 6 = 7`
- `7 ^ 7 = 0`
- `0 ^ 8 = 8`
The final answer is `8`.

---

### Example 2

**Input**

```java
L = 3, R = 7
```

**Output**

```java
3
```

**Explanation**

`3 ^ 4 ^ 5 ^ 6 ^ 7 = 3`.

---

## Brute Force Approach

Iterate through the range from `L` up to `R` sequentially, accumulating the XOR value inside a tracking register.

### Steps

1. Initialize an accumulator variable `result = 0`.
2. Run a loop starting from `i = L` up to `R`.
3. In each iteration, update the accumulator: `result ^= i`.
4. Return the final `result`.

### Complexity

```text
Time Complexity: O(R - L) // linear relative to range size
Space Complexity: O(1)
```

If `L = 1` and `R = 2 * 10^9`, a sequential traversal loop triggers an expensive bottleneck and causes a Time Limit Exceeded (TLE) error. We can compute this instantly in constant time by exploiting repeating prefix patterns.

---

# Optimal Approach: Prefix Pattern Matching in Constant Time

## Key Idea

We can compute the range XOR in **O(1) time** by utilizing prefix subtraction properties. Just like range sum queries, the XOR of numbers in `[L, R]` can be split into two separate prefix windows:

```text
XOR(L, R) = XOR(0, R) ^ XOR(0, L - 1)
```

This works because any element present in the range `[0, L - 1]` is included in both prefix pools and cancels out completely to `0` due to the identity `x ^ x = 0`.

### The Repeating Pattern of XOR(0, N)
If we write out the values of `XOR(0, N)` from 0 upward, a highly predictable pattern repeats every **4 integers**:

| N | Binary | Cumulative XOR(0, N) | Pattern Rule |
| :- | :--- | :--- | :--- |
| **0** | `0000` | `0` | If `N % 4 == 0`, return **N** |
| **1** | `0001` | `1` | If `N % 4 == 1`, return **1** |
| **2** | `0010` | `3` (N + 1) | If `N % 4 == 2`, return **N + 1** |
| **3** | `0011` | `0` | If `N % 4 == 3`, return **0** |
| **4** | `0100` | `4` | Pattern repeats... (N) |
| **5** | `0101` | `1` | Pattern repeats... (1) |

By implementing a simple O(1) modulo matching function to fetch `XOR(0, N)`, we can evaluate any arbitrary range `[L, R]` instantly using a single XOR calculation.

---

## Visual Understanding

Suppose `L = 4` and `R = 8`:

1. Compute `XOR(0, R)` where `R = 8`:
   - `8 % 4 == 0` \(\implies\) Pattern dictates it returns `N = 8`.
2. Compute `XOR(0, L - 1)` where `L - 1 = 3`:
   - `3 % 4 == 3` \(\implies\) Pattern dictates it returns `0`.
3. Combine the prefix windows using XOR:
   - `XOR(4, 8) = 8 ^ 0 = 8`.

The final answer `8` is found with zero loop processing.

---

## Partition Variables

Let:

```java
int xorTillR = computePrefixXor(R);
int xorTillLMinus1 = (L == 0) ? 0 : computePrefixXor(L - 1);
```

---

### Border Elements

If `L = 0`, the left prefix index parameter hits `-1`. We intercept this boundary directly using a ternary check to ensure negative inputs are never parsed into the pattern function.

---

## Correct Partition Condition

The sequence structure evaluates the target index layout via a simple 4-state branch check:

```java
int remainder = n % 4;
if (remainder == 0) return n;
if (remainder == 1) return 1;
if (remainder == 2) return n + 1;
return 0; // Remainder is 3
```

---

## Java Solution

```java
class Solution {

    public int rangeXor(int L, int R) {
        if (L > R) {
            return 0;
        }

        // XOR(L, R) = XOR(0, R) ^ XOR(0, L - 1)
        int xorTillR = computePrefixXor(R);
        int xorTillLMinus1 = (L == 0) ? 0 : computePrefixXor(L - 1);

        return xorTillR ^ xorTillLMinus1;
    }

    // Computes cumulative XOR from 0 up to n in O(1) time
    private int computePrefixXor(int n) {
        int remainder = n % 4;

        if (remainder == 0) {
            return n;
        } else if (remainder == 1) {
            return 1;
        } else if (remainder == 2) {
            return n + 1;
        } else {
            return 0; // Remainder is 3
        }
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Prefix Pattern Match** | **O(1)** | **O(1)** | Evaluates a fixed mathematical pattern sequence via direct remainder arithmetic, executing in constant time regardless of range width. |

---

## Key Insight

Leveraging the repeating cycle of binary prefixes cancels out the cost of full interval sweeps, converting an expensive range calculation into a constant-time math subtraction step.

```text
Time  : O(1) absolute constant step
Space : O(1) in-place register evaluation
```

---

## Similar Problems

1. Single Number (136)
2. Bitwise AND of Numbers Range (201)
3. Missing Number (268)
4. Power of Two (231)
5. Minimum Bit Flips to Convert Number (2220)
