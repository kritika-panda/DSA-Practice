# Repeated Substring Pattern

## Problem Statement

Given a string `s`, check if it can be constructed by taking a substring of it and repeating multiple times to form the exact original string.

The overall runtime complexity must be strictly bounded by:

```text
O(n)
```

*(where n is the length of the string s)*

---

## Examples

### Example 1

**Input**

```java
s = "abab"
```

**Output**

```java
true
```

**Explanation**

It is the substring `"ab"` repeated twice.

---

### Example 2

**Input**

```java
s = "aba"
```

**Output**

```java
false
```

---

### Example 3

**Input**

```java
s = "abcabcabcabc"
```

**Output**

```java
true
```

**Explanation**

It is the substring `"abc"` repeated four times or the substring `"abcabc"` repeated twice.

---

## Brute Force Approach

Iterate through all possible substring lengths `l` from `1` up to `n / 2`. For each divisor length, replicate the prefix substring `n / l` times and verify if it matches `s` exactly.

### Steps

1. Run a loop with length `l` from `1` to `n / 2`.
2. Check if `n % l == 0` (the length must divide the total string length perfectly).
3. If true, extract the substring component: `sub = s.substring(0, l)`.
4. Replicate `sub` to build a full-length string copy.
5. If the built string equals `s`, return `true`.

### Complexity

```text
Time Complexity: O(n * √n) or O(n^2) depending on divisor distribution and string generation
Space Complexity: O(n) to store the replicated string copies
```

Replicating and checking strings repeatedly creates massive allocation bottlenecks for highly structured data sets. We can solve this with an elegant math properties trick or a fast linear scan using Knuth-Morris-Pratt (KMP).

---

# Optimal Approach: String Doubling & Slicing Trick

## Key Idea

If a string `s` can be constructed entirely by repeating a smaller sub-pattern, then doubling the string to create `s + s` must encapsulate multiple hidden copies of the pattern.

If we clear away the absolute **first character** and the absolute **last character** of this combined `s + s` layout to form a new string `doubledString`, the original string `s` will **still be discoverable as an internal substring** inside it.

```text
Let doubledString = (s + s).substring(1, (s + s).length() - 1)
If s is a substring of doubledString -> Return true
```

If `s` cannot be formed by repeating a substring, clearing the boundary characters destroys the only integrity blocks that could align to form `s`, and a substring lookup will return `false`.

---

## Visual Understanding

Suppose `s = "abab"` with length 4:

1. **Double the String:**
   - `s + s = "abababab"`
2. **Prune Boundary Edges (Index 1 to 2n - 2):**
   - Remove first character `'a'` and final character `'b'`.
   - `doubledString = "bababa"`
3. **Substring Search:**
   - Does `"bababa"` contain our original `s` (`"abab"`)?
   - `"bababa"` \(\implies\) `"b" + "abab" + "a"`.
   - Yes! The match path is established successfully. Return `true`.

Suppose `s = "aba"` with length 3:
1. `s + s = "abaaba"`
2. Prune boundaries \(\implies\) `doubledString = "baab"`
3. Does `"baab"` contain `"aba"`? No. Return `false`.

---

## Partition Variables

Let:

```java
String doubled = s + s;
String pruned = doubled.substring(1, doubled.length() - 1);
```

---

### Border Elements

The method provides an immediate guard gate if the input length constraints fall below viable replication sizes:

```java
if (s == null || s.length() <= 1) {
    return false;
}
```

---

## Correct Partition Condition

The entire verification evaluates inside a single, clean sub-containment evaluation call:

```java
return pruned.contains(s);
```

---

## Java Solution

```java
class Solution {

    public boolean repeatedSubstringPattern(String s) {
        if (s == null || s.length() <= 1) {
            return false;
        }

        // Step 1: Concatenate the string with itself
        String doubled = s + s;

        // Step 2: Prune the absolute first and last characters
        String prunedTarget = doubled.substring(1, doubled.length() - 1);

        // Step 3: If original s exists inside the pruned target, it has a repeated pattern
        return prunedTarget.contains(s);
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **String Slicing Search** | **O(n)** | **O(n)** | String concatenation and `.contains()` lookups run in linear time utilizing high-performance string matching algorithms natively. |

---

## Key Insight

Doubling a periodic sequence and clearing the exterior boundary anchors reveals the internal periodic alignment instantly, turning a structural factorization loop into a single-pass substring verification.

```text
Time  : O(n) linear checking pass
Space : O(n) temporary string buffer allocations
```

---

## Similar Problems

1. Longest Happy Prefix (1392)
2. Find the Index of the First Occurrence in a String / KMP (28)
3. Rotate String (796)
4. Valid Anagram (242)
5. Z Algorithm Implementation
