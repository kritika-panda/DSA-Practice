# Greatest Common Divisor of Strings ([LeetCode 1071](https://leetcode.com))

## Problem Statement

For two strings `str1` and `str2`, we say "`t` divides `s`" if and only if `s` is equal to `t` concatenated with itself one or more times (e.g., if `t = "ABC"` and `s = "ABCABC"`, then `t` divides `s`).

Given two strings `str1` and `str2`, return *the largest string `x` such that `x` divides both `str1` and `str2`*.

---

## Examples

### Example 1

```text
Input: str1 = "ABCABC", str2 = "ABC"
Output: "ABC"
```

---

### Example 2

```text
Input: str1 = "ABABAB", str2 = "ABAB"
Output: "AB"
```

---

### Example 3

```text
Input: str1 = "LEET", str2 = "CODE"
Output: ""
```

---

# Intuition

This problem can be elegantly solved by drawing a parallel to finding the **Greatest Common Divisor (GCD)** of two numbers. 

1. **The Divisibility Invariant (Commutativity Check):** If a common divisor string `x` exists for both `str1` and `str2`, then concatenating `str1` and `str2` must yield the exact same result regardless of the order of concatenation. Formally, we must have:
   \[str1 + str2 == str2 + str1\]
   If this condition is false, it means the two strings are structurally incompatible (e.g., `"LEET"` and `"CODE"`), and no common divisor string can mathematically exist. In this case, we can return `""` immediately.
2. **Logarithmic Scale Sizing:** If they pass the compatibility check, the length of the greatest common divisor string **must be exactly equal to the GCD of the lengths of the two strings**. For example, if `str1` has a length of 6 (`"ABABAB"`) and `str2` has a length of 4 (`"ABAB"`), the greatest common divisor string will have a length of gcd(6, 4) = 2. The result is simply the prefix substring of that length: `"AB"`.

---

# State Definition

The structural relationship between the string blocks reduces down to basic length metrics:

```java
int gcdLength = gcd(str1.length(), str2.length()); // Calculate the numeric scalar GCD boundary
```

---

# Recurrence / Structural Euclidean Condition

```text
Function gcd(a, b):
    While b != 0:
        int temp = b
        b = a % b
        a = temp
    Return a
```

---

# Optimal Logarithmic Euclidean Solution

## Idea

We first check if `str1 + str2` equals `str2 + str1` to verify compatibility. If it passes, we use the standard iterative **Euclidean Algorithm** to calculate the mathematical GCD of their lengths. We then slice out the prefix of `str1` matching that calculated length as our greatest common divisor string.

---

## Java Code

```java
class Solution {
    public String gcdOfStrings(String str1, String str2) {
        // Step 1: Invariant Check - Verify if the strings share a compatible structural baseline
        // If concatenating them in different orders yields different results, no common divisor exists
        if (!(str1 + str2).equals(str2 + str1)) {
            return "";
        }

        // Step 2: Calculate the Greatest Common Divisor of their lengths
        int gcdLength = gcd(str1.length(), str2.length());

        // Step 3: The GCD string is simply the prefix substring matching the calculated GCD length
        return str1.substring(0, gcdLength);
    }

    // High-speed iterative implementation of the Euclidean Algorithm
    private int gcd(int a, int b) {
        while (b != 0) {
            int temp = b;
            b = a % b;
            a = temp;
        }
        return a;
    }
}
```

---

## Complexity Analysis

Let N and M be the character lengths of `str1` and `str2` respectively.

### Time Complexity

```text
Time Complexity: O(N + M)
```
*Explanation:* The structural compatibility invariant check creates a combined string of length N + M and compares it, which takes linear O(N + M) time. The numeric Euclidean algorithm runs in logarithmic \(O(\log(\min(N, M)))\) time. Combining these phases keeping operations bounded linearly by the string lengths.

---

### Space Complexity

```text
Space Complexity: O(N + M)
```
*Explanation:* String concatenation (`str1 + str2`) allocates memory on the heap proportional to the combined lengths of the inputs. The scalar Euclidean algorithm and variable registers run inside an optimal O(1) constant auxiliary space.

---

# Key Takeaways

✅ Core fundamental computing template for text sequence alignment and numerical string reductions.

✅ **The Commutativity Rule:** Verifying `(str1 + str2).equals(str2 + str1)` is a highly strategic line of code. It checks for a shared periodic root pattern across the strings. If they do not commute under concatenation, they do not share a common divisor.

✅ Directly drives real-world microarchitectures like **data streaming compression protocol modules** (identifying repetitive periodic data frames to flatten network packet sizes), text pattern tokenizers, audio waveform cycle matchers, and telemetry parsers.

### Similar Problems

- Repeated Substring Pattern ([LeetCode 459](https://leetcode.com))
- Greatest Common Divisor of Array ([LeetCode 1979](https://leetcode.com))
- Longest Common Prefix ([LeetCode 14](https://leetcode.com))
