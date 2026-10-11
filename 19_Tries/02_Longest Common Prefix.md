# Longest Common Prefix

## Problem Statement

Write a function to find the longest common prefix string amongst an array of strings.

If there is no common prefix, return an empty string `""`.

The overall run time complexity should be:

```text
O(n * l)
```

*(where n is the number of strings and l is the length of the shortest string)*

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

**Explanation**

The character sequence "fl" is the longest shared starting prefix among all three input strings.

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

There is no common prefix among the input strings, so an empty string is returned.

---

### Example 3

**Input**

```java
strs = ["apple", "apple", "apple"]
```

**Output**

```java
"apple"
```

---

## Brute Force Approach

Compare characters horizontally at each vertical index position across all strings simultaneously.

### Steps

1. Take the first string as a baseline reference.
2. Iterate through each character of the first string at index `i`.
3. Check if index `i` is within bounds for all other strings and if the character matches.
4. If a mismatch is found or a string runs out of characters, return the substring of the first string from index `0` to `i`.
5. If the loops finish completely, return the entire first string.

### Complexity

```text
Time Complexity: O(s) // where s is the total sum of all characters across all strings
Space Complexity: O(1)
```

While this horizontal scan is optimal, we can also frame it as a dynamic reduction problem by comparing strings sequentially using a sliding window match.

---

# Optimal Approach: Iterative Reduction / Horizontal Scanning

## Key Idea

Instead of jumping across all strings character by character, we can greedily assume the entire first string is our `prefix`.

We then compare this `prefix` against the second string. If the second string does not start with our `prefix`, we shorten the `prefix` by chopping off its last character, one character at a time, until the second string matches the prefix.

We repeat this reduction step sequentially for every string in the array:

```text
Initial Prefix = strs[0]
Loop through strs[1] to strs[n-1]:
    Shrink prefix until strs[i].startsWith(prefix) == true
```

If at any point our `prefix` shrinks to an empty string `""`, we can immediately terminate the loop and return `""`, as no common prefix exists.

---

## Visual Understanding

Suppose:

```java
strs = ["flower", "flow", "flight"]
```

1. **Initialize Prefix:**
   - `prefix = "flower"`

2. **Compare with `strs[1]` ("flow"):**
   - `"flow".startsWith("flower")` is false. Shrink -> `"flowe"`
   - `"flow".startsWith("flowe")` is false. Shrink -> `"flow"`
   - `"flow".startsWith("flow")` is true! Update `prefix = "flow"`.

3. **Compare with `strs[2]` ("flight"):**
   - `"flight".startsWith("flow")` is false. Shrink -> `"flo"`
   - `"flight".startsWith("flo")` is false. Shrink -> `"fl"`
   - `"flight".startsWith("fl")` is true! Update `prefix = "fl"`.

Final Result = `"fl"`.

---

## Partition Variables

Let:

```java
String prefix = strs[0];
int i = 1;
```

---

### Border Elements

The inner loop uses `indexOf` or `startsWith` to check if the current prefix is present at the absolute start of the string:

```java
while (strs[i].indexOf(prefix) != 0) {
    prefix = prefix.substring(0, prefix.length() - 1);
}
```

---

## Correct Partition Condition

If the prefix matches down to index 0, the current string satisfies the prefix constraint, and we advance to the next element:

```java
if (prefix.isEmpty()) return "";
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitioning with an iterative string reduction approach to optimize string matching overhead).*

---

## Java Solution

```java
class Solution {

    public String longestCommonPrefix(String[] strs) {

        if (strs == null || strs.length == 0) {
            return "";
        }

        // Step 1: Assume the first string is the initial prefix candidate
        String prefix = strs[0];

        // Step 2: Compare the running prefix against each string sequentially
        for (int i = 1; i < strs.length; i++) {
            
            // Shrink the prefix from the right side until it matches the start of strs[i]
            while (strs[i].indexOf(prefix) != 0) {
                prefix = prefix.substring(0, prefix.length() - 1);
                
                // If the prefix becomes empty, there is no common prefix
                if (prefix.isEmpty()) {
                    return "";
                }
            }
        }

        return prefix;
    }
}
```

---

## Dry Run

### Input

```java
strs = ["flower", "flow", "flight"]
```

---

### Step Execution Traversal

- **i = 1 (`strs = "flow"`):**
  - `"flow".indexOf("flower")` is `-1` (not 0). `prefix = "flowe"`.
  - `"flow".indexOf("flowe")` is `-1`. `prefix = "flow"`.
  - `"flow".indexOf("flow")` is `0`. Loop ends for `i = 1`.
- **i = 2 (`strs = "flight"`):**
  - `"flight".indexOf("flow")` is `-1`. `prefix = "flo"`.
  - `"flight".indexOf("flo")` is `-1`. `prefix = "fl"`.
  - `"flight".indexOf("fl")` is `0`. Loop ends for `i = 2`.

Outer loop finishes completely.

---

### Answer

```java
"fl"
```

---

## Why Do We Use Iterative Reduction?

By matching strings sequentially, we avoid checking index boundaries across multiple dimensions simultaneously. If any string breaks the prefix pattern early on, the prefix is truncated immediately, preventing unnecessary scans across the remaining elements.

This greedy pruning yields:

```text
O(n * l)
```

which satisfies the complexity constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n * l)
```

Where `n` is the number of strings in the array and `l` is the length of the first string. In the worst-case scenario, every string is scanned completely, resulting in a runtime proportional to the total number of characters.

---

### Space Complexity

```text
O(1)
```

The matching updates are performed in-place using variable references. No additional data arrays are allocated.

---

## Key Insight

Assuming the best-case configuration initially and shrinking it progressively upon encountering failures reduces nested tracking states into a clean linear evaluation.

```text
Time  : O(n * l)
Space : O(1)
```

---

## Similar Problems

1. Longest Common Subsequence (1143)
2. Implement Trie (Prefix Tree) (208)
3. String to Integer (atoi) (8)
4. Valid Palindrome (125)
5. Find the Index of the First Occurrence in a String (28)
