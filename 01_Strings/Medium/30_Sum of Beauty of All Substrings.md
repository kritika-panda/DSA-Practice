# Sum of Beauty of All Substrings

**LeetCode 1781 - Sum of Beauty of All Substrings**

## Problem Statement

The **beauty** of a string is the difference between the frequency of the most frequent character and the least frequent character.
* For example, the beauty of `"abaacc"` is `3 - 1 = 2` because the most frequent character is `'a'` (frequency 3) and the least frequent character is `'b'` (frequency 1).

Given a string `s`, return the **sum of beauty of all its substrings**.

---

## Example

```java
String s = "aabcb";

Solution sol = new Solution();
sol.beautySum(s); // Returns 5
```

**Explanation:** The substrings with non-zero beauty are:
* `"aabc"` -> beauty = `2 - 1 = 1`
* `"aabcb"` -> beauty = `3 - 1 = 2`
* `"abcb"` -> beauty = `2 - 1 = 1`
* `"bcb"` -> beauty = `2 - 1 = 1`

The total sum of beauty is `1 + 2 + 1 + 1 = 5`.

---

# Intuition

A naive brute force approach would generate all (O(N^2)) substrings explicitly, extract each slice, and compute frequencies from scratch for each substring. Since finding the frequency of characters in a slice takes (O(N)) time, this would cost (O(N^3)) total time — leading to a Time Limit Exceeded (TLE) error.

We can optimize this to (O(N^2)) by using a **Simulated Window Expansion** technique. Instead of recreating the frequency distribution from scratch for every substring, we can fix a starting index `i` and expand a window to the right using index `j`. 

As `j` moves forward, we can update the frequency of the incoming character `s.charAt(j)` in **(O(1)) constant time**. Since the alphabet size is strictly limited to lowercase English letters (26 choices), calculating the maximum and minimum frequencies from our frequency array at any step takes an invariant (O(26) = O(1)) operations.

---

# Approach: Character Frequency Counter Optimization

## Data Structure

```java
int[] freq = new int[26]
```
An integer array used to track the counts of lowercase English alphabets dynamically for the current substring window starting at index `i`.

---

## Algorithm Steps

1. Initialize `totalBeautySum = 0`.
2. Run an outer loop with pointer `i` from `0` to `N - 1` to mark the starting position of our substrings.
3. For each unique starting position `i`, reset the character frequency bucket array `freq` to all zeroes.
4. Run an inner loop with pointer `j` from `i` to `N - 1` to expand the substring window:
   - Increment the frequency of the current character: `freq[s.charAt(j) - 'a']++`.
   - Call a helper routine to scan the active buckets in `freq` to fetch the current `maxFrequency` and `minFrequency` (ignoring zero entries).
   - Compute the instantaneous beauty: `beauty = maxFrequency - minFrequency`.
   - Add `beauty` to `totalBeautySum`.
5. Return `totalBeautySum` after checking all substring boundaries.

---

# Dry Run

### Input
```java
s = "aab", N = 3
```

### Execution Trace

- **Initialization:** `totalBeautySum = 0`

| Outer `i` | Inner `j` | Substring | updated `freq` | Max Freq | Min Freq (Non-zero) | Beauty (`Max - Min`) | `totalBeautySum` |
|---|---|---|---|---|---|---|---|
| `0` | `0` | `"a"` | `{'a': 1}` | `1` | `1` | `1 - 1 = 0` | `0` |
| `0` | `1` | `"aa"` | `{'a': 2}` | `2` | `2` | `2 - 2 = 0` | `0` |
| `0` | `2` | `"aab"` | `{'a': 2, 'b': 1}` | `2` | `1` | `2 - 1 = 1` | `0 + 1 = 1` |
| | | | | | | | |
| `1` | `1` | `"a"` | `{'a': 1}` | `1` | `1` | `1 - 1 = 0` | `1` |
| `1` | `2` | `"ab"` | `{'a': 1, 'b': 1}` | `1` | `1` | `1 - 1 = 0` | `1` |
| | | | | | | | |
| `2` | `2` | `"b"` | `{'b': 1}` | `1` | `1` | `1 - 1 = 0` | `1` |

### Result
```java
totalBeautySum = 1
```

---

# Java Solution

```java
class Solution {
    public int beautySum(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }

        int n = s.length();
        int totalBeautySum = 0;

        // Loop over all possible starting positions of substrings
        for (int i = 0; i < n; i++) {
            int[] freq = new int[26];

            // Expand the substring window to the right
            for (int j = i; j < n; j++) {
                freq[s.charAt(j) - 'a']++;

                // Calculate beauty for the current substring s[i...j]
                totalBeautySum += getBeauty(freq);
            }
        }

        return totalBeautySum;
    }

    private int getBeauty(int[] freq) {
        int maxF = Integer.MIN_VALUE;
        int minF = Integer.MAX_VALUE;

        for (int f : freq) {
            if (f > 0) {
                maxF = Math.max(maxF, f);
                minF = Math.min(minF, f);
            }
        }

        return (maxF == Integer.MIN_VALUE) ? 0 : (maxF - minF);
    }
}
```

---

# Complexity Analysis

### Time Complexity

```java
O(N²)
```
The nested loops examine (O(N^2)) unique substring boundaries. Inside the inner loop, updating the frequency takes (O(1)) time. Scanning the alphabet grid to find the maximum and minimum frequencies takes a constant (O(26)) operations. Therefore, the overall time scales as (O(26 cdot N^2) = O(N^2)).

### Space Complexity

```java
O(1)
```
The frequency tracking table allocation is hard-capped at an unvarying constraint of `26` integers regardless of input length, keeping our runtime environment purely constant.

---

# Comparison of Approaches

| Approach | Time Complexity | Space Complexity | Pros / Cons |
|---|---|---|---|
| **Brute Force Extract** | (O(N^3)) | (O(N)) | Conceptually lazy; runs into a harsh TLE block for larger strings. |
| **Incremental Frequency Counter** | (O(N^2)) | (O(1)) | **Highly optimal.** Drastically lowers timing barriers by avoiding redundant subsegment scans. |

---

# Key Takeaways

- **Avoid Redundant Recomputations:** When dealing with comprehensive nested substring evaluations, design loops so that state variables gracefully inherit updates from step `j-1` instead of rebuilding profiles.
- **Constant Sized Metric Scans:** An internal loop scanning an array size of 26 maps directly to an (O(1)) asymptotic complexity bound.
- Core concepts:
  - Substring Metric Rolling States
  - Fixed-Size Frequency Bucketing
  - Asymptotic Optimization via Loop Merges
- Expected interview preference: **Incremental Frequency Counter Approach**.
