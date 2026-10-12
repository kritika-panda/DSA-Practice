# Is Subsequence

## Problem Statement

Given two strings `s` and `t`, return `true` *if `s` is a **subsequence** of `t`, or `false` otherwise*.

A **subsequence** of a string is a new string that is formed from the original string by deleting some (can be none) of the characters without disturbing the relative positions of the remaining characters. (i.e., `"ace"` is a subsequence of `"abcde"` while `"aec"` is not).

The overall runtime complexity must be strictly bounded by:

```text
O(n)
```

*(where n is the length of string t)*

---

## Examples

### Example 1

**Input**

```java
s = "abc"
t = "ahbgdc"
```

**Output**

```java
true
```

**Explanation**

The characters `'a'`, `'b'`, and `'c'` appear sequentially inside `"ahbgdc"` without disturbing their relative order.

---

### Example 2

**Input**

```java
s = "axc"
t = "ahbgdc"
```

**Output**

```java
false
```

**Explanation**

The character `'x'` does not exist in `t`, so `s` cannot be a subsequence.

---

### Example 3

**Input**

```java
s = ""
t = "ahbgdc"
```

**Output**

```java
true
```

**Explanation**

An empty string is a valid subsequence of any string.

---

## Brute Force Approach

Use recursive backtracking to generate all \(2^n\) possible subsequences of `t` and check if any generated string matches `s`.

### Steps

1. Launch a recursive helper function to generate every valid subsequence combinations of `t`.
2. Save each result to a global verification pool.
3. Check if `s` exists in the pool of generated combinations.

### Complexity

```text
Time Complexity: O(2^n) // exponential scaling layer
Space Complexity: O(n) for the recursive call stack depth
```

This exponential combinatorial scaling causes an immediate Time Limit Exceeded (TLE) error for long target strings. Since relative positions are fixed, we can solve this problem in a single pass using two pointers.

---

# Optimal Approach: Two-Pointer Linear Matching Sweep

## Key Idea

We can solve this problem in **linear time** using a simple **Two-Pointer technique**.

1. Place a pointer `i` at the start of string `s` (index `0`) and a pointer `j` at the start of string `t` (index `0`).
2. Run a loop that continues as long as `i < s.length()` and `j < t.length()`.
3. In each step, check if the characters at the current pointers match:
   - **If they match (`s.charAt(i) == t.charAt(j)`):** We have found the current character of our subsequence. Advance the subsequence pointer `i` forward by 1 to look for the next required letter.
   - **Always advance `j`:** Regardless of whether there is a match or mismatch, advance the main text pointer `j` forward by 1 to keep scanning the text.
4. **Termination Check:** If the loop terminates and `i == s.length()`, it means all characters of `s` were successfully discovered sequentially inside `t`.

---

## Visual Understanding

Suppose `s = "abc"` and `t = "ahbgdc"`:

- **Initialization:** `i = 0` (pointing to `'a'`), `j = 0` (pointing to `'a'`).
- **Step 1:** `s[0] ('a') == t[0] ('a')` \(\implies\) Match! `i` becomes 1, `j` becomes 1.
- **Step 2:** `s[1] ('b') != t[1] ('h')` \(\implies\) Mismatch. `j` becomes 2.
- **Step 3:** `s[1] ('b') == t[2] ('b')` \(\implies\) Match! `i` becomes 2, `j` becomes 3.
- **Step 4:** `s[2] ('c') != t[3] ('g')` \(\implies\) Mismatch. `j` becomes 4.
- **Step 5:** `s[2] ('c') != t[4] ('d')` \(\implies\) Mismatch. `j` becomes 5.
- **Step 6:** `s[2] ('c') == t[5] ('c')` \(\implies\) Match! `i` becomes 3, `j` becomes 6.
- **Termination:** `i == s.length() (3)`. All characters found! Return `true`.

---

## Partition Variables

Let:

```java
int i = 0; // Pointer for subsequence s
int j = 0; // Pointer for target text t
```

---

### Border Elements

An early optimization instantly checks if the length of the subsequence `s` exceeds the available capacity of `t`:

```java
if (s.length() > t.length()) {
    return false;
}
```

---

## Correct Partition Condition

The linear parallel sweep loops safely within the index lengths of both string parameters:

```java
while (i < s.length() && j < t.length()) {
    if (s.charAt(i) == t.charAt(j)) {
        i++;
    }
    j++;
}
return i == s.length();
```

---

## Java Solution

```java
class Solution {

    public boolean isSubsequence(String s, String t) {
        if (s == null || t == null) {
            return false;
        }

        // Fast-fail: Subsequence cannot be longer than the target text
        if (s.length() > t.length()) {
            return false;
        }

        int i = 0; // Pointer for string s
        int j = 0; // Pointer for string t

        // Linear sweep across string t
        while (i < s.length() && j < t.length()) {
            // If characters match, advance our subsequence pointer
            if (s.charAt(i) == t.charAt(j)) {
                i++;
            }
            // Main text pointer always advances forward
            j++;
        }

        // Returns true if all characters of s were successfully matched
        return i == s.length();
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Two-Pointer Sweep** | **O(n)** | **O(1)** | Where `n` is the length of string `t`. The pointer `j` advances across the characters of `t` exactly once in a single forward pass. |

---

## Key Insight

Advancing a secondary pointer only upon confirming character matches allows relative sequence order to be verified in a single linear pass with absolute zero extra memory allocation overhead.

```text
Time  : O(n) single pass forward execution
Space : O(1) absolute constant memory tracking footprint
```

---

## Similar Problems

1. Number of Matching Subsequences (792)
2. Longest Palindromic Subsequence (516)
3. Distinct Subsequences (115)
4. Shortest Way to Form String (1055)
5. Append Characters to String to Make Subsequence (2486)
