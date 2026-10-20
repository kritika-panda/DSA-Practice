# Longest Common Prefix

## Problem Statement

Write a function to find the **longest common prefix string** amongst an array of strings.

If there is no common prefix, return an empty string `""`.

### Example 1

```text
Input:
["flower","flow","flight"]

Output:
"fl"
```

### Example 2

```text
Input:
["dog","racecar","car"]

Output:
""
```

---

# Optimal Approach: Vertical Scanning

## Intuition

Instead of repeatedly shrinking a prefix candidate, we compare characters **column by column**.

Think of each string as being arranged vertically:

```text
Index →   0   1   2   3   4   5

flower    f   l   o   w   e   r
flow      f   l   o   w
flight    f   l   i   g   h   t
```

### Compare Column-by-Column

```text
Index 0

flower -> f
flow   -> f
flight -> f

All match ✅
```

```text
Index 1

flower -> l
flow   -> l
flight -> l

All match ✅
```

```text
Index 2

flower -> o
flow   -> o
flight -> i

Mismatch ❌
```

The common prefix is:

```text
"fl"
```

---

# Algorithm

### Step 1

Use the first string as the reference string.

```java
strs[0]
```

### Step 2

Traverse every character position of the first string.

```java
for (int i = 0; i < strs[0].length(); i++)
```

---

### Step 3

Store the current character.

```java
char currentChar = strs[0].charAt(i);
```

---

### Step 4

Check this character against all remaining strings.

```java
for (int j = 1; j < strs.length; j++)
```

---

### Step 5

Stop if:

#### Condition 1

Current string ends before reaching index `i`.

```java
i == strs[j].length()
```

Example:

```text
flow
fl
flower
```

At:

```text
i = 2
```

String `"fl"` has ended.

Return:

```text
"fl"
```

---

#### Condition 2

Characters do not match.

```java
strs[j].charAt(i) != currentChar
```

Example:

```text
flower
flow
flight
```

At:

```text
i = 2

o != i
```

Return:

```text
"fl"
```

---

# Java Solution

```java
class Solution {

    public String longestCommonPrefix(String[] strs) {

        if (strs == null || strs.length == 0) {
            return "";
        }

        for (int i = 0; i < strs[0].length(); i++) {

            char currentChar = strs[0].charAt(i);

            for (int j = 1; j < strs.length; j++) {

                if (i == strs[j].length() ||
                    strs[j].charAt(i) != currentChar) {

                    return strs[0].substring(0, i);
                }
            }
        }

        return strs[0];
    }
}
```

---

# Dry Run

## Input

```java
["flower", "flow", "flight"]
```

Initial:

```text
Reference String = "flower"
```

---

### Iteration 1

```text
i = 0
currentChar = 'f'
```

Compare:

```text
flow   -> f ✅
flight -> f ✅
```

Continue.

---

### Iteration 2

```text
i = 1
currentChar = 'l'
```

Compare:

```text
flow   -> l ✅
flight -> l ✅
```

Continue.

---

### Iteration 3

```text
i = 2
currentChar = 'o'
```

Compare:

```text
flow   -> o ✅
flight -> i ❌
```

Mismatch found.

Return:

```java
strs[0].substring(0, 2)
```

Result:

```text
"fl"
```

---

# Why Is This Optimal?

The inefficient approach repeatedly shrinks prefixes:

```text
flower
flowe
flow
flo
fl
```

Many characters get examined multiple times.

---

This solution compares each character position only once:

```text
f ✅
l ✅
o ❌
```

No repeated work.

---

# Complexity Analysis

## Variable Definitions

Let:

```text
n = number of strings
m = length of shortest string
S = total number of characters across all strings
```

---

## Line-by-Line Complexity

### Empty Check

```java
if (strs == null || strs.length == 0)
```

Time Complexity:

```text
O(1)
```

Space Complexity:

```text
O(1)
```

---

### Outer Loop

```java
for (int i = 0; i < strs[0].length(); i++)
```

Runs at most:

```text
m times
```

Time Complexity:

```text
O(m)
```

---

### Character Access

```java
char currentChar = strs[0].charAt(i);
```

Time Complexity:

```text
O(1)
```

---

### Inner Loop

```java
for (int j = 1; j < strs.length; j++)
```

Runs through all strings.

Time Complexity:

```text
O(n)
```

---

### Character Comparison

```java
strs[j].charAt(i) != currentChar
```

Time Complexity:

```text
O(1)
```

---

### String-End Check

```java
i == strs[j].length()
```

Time Complexity:

```text
O(1)
```

---

## Total Time Complexity

Every valid character position from every string is inspected at most once.

Thus:

```text
O(S)
```

where:

```text
S = total number of characters across all strings
```

Equivalent representation:

```text
O(n × m)
```

---

## Total Space Complexity

Only a few variables are used:

```java
i
j
currentChar
```

No extra data structures are created.

```text
O(1)
```

---

# Complexity Table

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| Empty Check | O(1) | O(1) | Constant-time validation. |
| Character Access (`charAt`) | O(1) | O(1) | Direct index lookup. |
| Character Comparison | O(1) | O(1) | Single-character comparison. |
| Vertical Scanning | O(S) | O(1) | Each character across all strings is examined at most once. |

---

# Final Complexity

```text
Time Complexity  : O(S)

Space Complexity : O(1)
```

Where:

```text
S = Total number of characters across all strings.
```

This is the optimal interview-preferred solution for **LeetCode 14: Longest Common Prefix**.
