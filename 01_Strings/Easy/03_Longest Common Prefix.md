# Longest Common Prefix

## Problem Statement

Write a function to find the **longest common prefix** string amongst an array of strings.

If there is no common prefix, return an empty string `""`.

The overall runtime complexity must be:

```text
O(S)
```

*(where S is the sum of all characters in all strings inside the input array)*

---

## Examples

### Example 1

**Input**

```java
strs = ["flower", "flow", "flight"]
```

**Output**

```java
"fl"
```

---

### Example 2

**Input**

```java
strs = ["dog", "racecar", "car"]
```

**Output**

```java
""
```

**Explanation**

There is no common prefix among the input strings.

---

### Example 3

**Input**

```java
strs = ["interspecies", "interstellar", "interstate"]
```

**Output**

```java
"inters"
```

---

## Brute Force Approach

Compare characters horizontally at index `0`, then index `1`, index `2`, and so on across all strings until a mismatch or out-of-bounds index is hit.

### Steps

1. Pick the first string as a reference window.
2. Run an outer loop tracking index `i` from `0` to the length of the reference string.
3. Run an inner loop from index `1` to `strs.length - 1` to check if `strs[j].charAt(i)` matches the reference character.
4. If `i` reaches the end of *any* string or a character mismatch is hit, slice the reference string from index `0` up to `i` and return it.

### Complexity

```text
Time Complexity: O(S) // where S is the sum of characters across all strings
Space Complexity: O(1) auxiliary space
```

While horizontal scanning is functionally correct, it forces multiple string checks per index layer. We can simplify this dramatically by using a **vertical scanning prefix-reduction technique**.

---

# Optimal Approach: Vertical Prefix Reduction

## Key Idea

We can find the common prefix by starting with the **entire first string** as our initial prefix candidate. We then greedily shorten this prefix candidate as we compare it against each subsequent string in the array.

1. Initialize `prefix = strs[0]`.
2. Iterate through the remaining strings from index `1` to `strs.length - 1`.
3. Check if the current string starts with our active `prefix` using `.indexOf(prefix) == 0`.
4. If it doesn't match, chop off the very last character of the `prefix` candidate string using `prefix = prefix.substring(0, prefix.length() - 1)`.
5. Repeat the chopping step until the current string matches our prefix. If the prefix shrinks to an empty string `""`, it means there is no shared prefix, and we can exit early.

---

## Visual Understanding

Suppose `strs = ["flower", "flow", "flight"]`:

- **Initialize:** `prefix = "flower"`.
- **Compare with "flow":**
  - `"flow".indexOf("flower")` is not 0. Chop! `prefix = "flowe"`.
  - `"flow".indexOf("flowe")` is not 0. Chop! `prefix = "flow"`.
  - `"flow".indexOf("flow") == 0`. Match confirmed. Move to the next string.
- **Compare with "flight":**
  - `"flight".indexOf("flow")` is not 0. Chop! `prefix = "flo"`.
  - `"flight".indexOf("flo")` is not 0. Chop! `prefix = "fl"`.
  - `"flight".indexOf("fl") == 0`. Match confirmed. Loop completes.

The final calculated common prefix is `"fl"`.

---

## Partition Variables

Let:

```java
String prefix = strs[0];
```

---

### Border Elements

The boundary verification cuts loops short instantly if the prefix state shrinks down to an empty string `""`:

```java
if (prefix.isEmpty()) {
    return "";
}
```

---

## Correct Partition Condition

The shrinking mechanism continuously strips characters from the tail of the string block until a matching prefix match path is established:

```java
while (strs[i].indexOf(prefix) != 0) {
    prefix = prefix.substring(0, prefix.length() - 1);
    if (prefix.isEmpty()) return "";
}
```

---

## Java Solution

```java
class Solution {

    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) {     // O(1)
            return "";   // O(1)
        }

        // Step 1: Start with the first string as our base prefix candidate
        String prefix = strs[0]; // O(1)

        // Step 2: Compare and reduce the prefix across the remaining strings
        for (int i = 1; i < strs.length; i++) {    // O(n)
            
            // Chop off the trailing character until strs[i] starts with the prefix
            while (strs[i].indexOf(prefix) != 0) {    // O(k)
                prefix = prefix.substring(0, prefix.length() - 1);   // O(k)
                
                // Fast-fail: If the prefix shrinks to empty, no common match exists
                if (prefix.isEmpty()) { // O(1)
                    return ""; // O(1)
                }
            }
        }

        return prefix;  // O(1)
    }
}
```

---

# Why Doesn't `indexOf()` Make This O(n × m³)?

You're absolutely right that **general substring search** is often analyzed as:

```text
indexOf(pattern)
O(N × M)
```

where:

- `N` = text length
- `M` = pattern length

But in **this specific code**, we're doing:

```java
strs[i].indexOf(prefix) != 0
```

not:

```java
strs[i].contains(prefix)
```

or searching for the pattern anywhere within the string.

---

## What Are We Really Checking?

Suppose:

```java
strs[i] = "flight";
prefix  = "flower";
```

When Java evaluates:

```java
"flight".indexOf("flower")
```

it compares:

```text
position 0:
f == f ✓
l == l ✓
i != o ✗
```

The mismatch occurs very quickly.

Effectively, this usage behaves like:

```java
strs[i].startsWith(prefix)
```

whose cost is:

```text
O(prefix.length())
```

---

## LeetCode / Interview Analysis

Let:

- `n` = number of strings
- `m` = maximum string length

For each iteration of the `while` loop:

```java
strs[i].indexOf(prefix)
```

is treated as:

```text
O(m)
```

and:

```java
prefix = prefix.substring(...)
```

is also:

```text
O(m)
```

The prefix can shrink at most:

```text
m times
```

So:

```text
O(m + (m-1) + (m-2) + ... + 1)
= O(m²)
```

For all strings:

```text
O(n × m²)
```

Therefore:

```text
Time Complexity: O(n × m²)
Space Complexity: O(1)
```

---

## Strict Library-Method Analysis

If we treat:

```java
indexOf(prefix)
```

as a general substring search, then:

```text
indexOf(prefix) = O(textLength × prefixLength)
```

Let:

- text length = m
- prefix length = m

Then:

```text
indexOf(prefix) = O(m²)
```

Each `while` iteration becomes:

```text
O(m²) + O(m)
≈ O(m²)
```

Since the prefix may shrink from:

```text
m → m-1 → m-2 → ... → 1
```

we perform approximately:

```text
m iterations
```

Thus:

```text
O(m × m²)
= O(m³)
```

Across all strings:

```text
O(n × m³)
```

---

## Which Complexity Should You Mention in an Interview?

Most interviewers and LeetCode editorials assume:

```java
strs[i].indexOf(prefix) != 0
```

is effectively being used as:

```java
strs[i].startsWith(prefix)
```

Therefore, the accepted complexity is:

```text
Time Complexity: O(n × m²)
Space Complexity: O(1)
```

If you're performing a strict low-level analysis of the library method implementation, you could argue:

```text
Time Complexity: O(n × m³)
```

However, for interviews and coding platforms, **O(n × m²)** is the expected answer.

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Prefix Filtering** | **O(S)** | **O(1)** | Where `S` is the total character payload across all strings. In the worst-case scenario, the algorithm inspects all string components. |

---

## Key Insight

Treating the first element as a global prefix window and slicing characters backward based on instant index boundaries prevents complex character matrix indexing loops.

```text
Time  : O(S) linear character lookup
Space : O(1) constant in-place reference tracking
```

---

## Similar Problems

1. Longest Common Subsequence (1143)
2. String to Integer / atoi (8)
3. Count and Say (38)
4. Valid Parentheses (20)
5. Find the Index of the First Occurrence in a String / KMP (28)
