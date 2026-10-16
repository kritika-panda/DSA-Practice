# Remove Duplicate Characters

## Problem Statement

Given a string `s`, remove duplicate characters while preserving the order of their first occurrence.

Return the resulting string containing only distinct characters.

---

## Example 1

```text
Input:
s = "programming"

Output:
progamin
```

Explanation:

```text
Characters r, m, g appear multiple times.

Keep only their first occurrence.
```

---

## Example 2

```text
Input:
s = "aabbcc"

Output:
abc
```

---

## Example 3

```text
Input:
s = "abcd"

Output:
abcd
```

No duplicate characters exist.

---

# Intuition

While traversing the string:

```text
If character has not appeared before
→ Add it to result

If character is already seen
→ Skip it
```

We need a fast way to determine whether a character has already been processed.

---

# Data Structure Choice

Use:

```java
HashSet<Character>
```

to track visited characters.

Benefits:

```text
Insert  → O(1)
Search  → O(1)
```

---

# Algorithm

For each character:

```text
1. Check if character exists in HashSet.
2. If not present:
      Add to HashSet
      Append to StringBuilder
3. Otherwise:
      Skip character.
```

---

# Visualization

Input:

```text
programming
```

Processing:

```text
p → add
r → add
o → add
g → add
r → skip
a → add
m → add
m → skip
i → add
n → add
g → skip
```

Result:

```text
progamin
```

---

# Java Code

```java
import java.util.*;

public class Main {

    public static String removeDuplicates(String s) {

        Set<Character> seen =
                new HashSet<>();

        StringBuilder result =
                new StringBuilder();

        for (char ch : s.toCharArray()) {

            if (!seen.contains(ch)) {

                seen.add(ch);
                result.append(ch);
            }
        }

        return result.toString();
    }

    public static void main(String[] args) {

        String s = "programming";

        System.out.println(
                removeDuplicates(s)
        );
    }
}
```

---

# Dry Run

## Input

```text
s = "aabbcc"
```

---

### Iteration 1

```text
a
```

Seen:

```text
{}
```

Add:

```text
result = "a"
```

---

### Iteration 2

```text
a
```

Already seen.

Skip.

```text
result = "a"
```

---

### Iteration 3

```text
b
```

Add.

```text
result = "ab"
```

---

### Iteration 4

```text
b
```

Skip.

---

### Iteration 5

```text
c
```

Add.

```text
result = "abc"
```

---

### Iteration 6

```text
c
```

Skip.

---

Final Answer:

```text
abc
```

---

# Step-by-Step Table

| Character | Seen Before? | Action | Result |
|------------|-------------|---------|---------|
| a | No | Add | a |
| a | Yes | Skip | a |
| b | No | Add | ab |
| b | Yes | Skip | ab |
| c | No | Add | abc |
| c | Yes | Skip | abc |

---

# Alternative Approach Using Boolean Array

If the string contains only lowercase English letters:

```java
boolean[] visited = new boolean[26];
```

instead of:

```java
HashSet<Character>
```

This reduces extra overhead.

---

# Java Code (Boolean Array)

```java
public class Main {

    public static String removeDuplicates(String s) {

        boolean[] visited =
                new boolean[26];

        StringBuilder result =
                new StringBuilder();

        for (char ch : s.toCharArray()) {

            if (!visited[ch - 'a']) {

                visited[ch - 'a'] = true;
                result.append(ch);
            }
        }

        return result.toString();
    }
}
```

---

# Time Complexity

Traversal of string:

```text
O(N)
```

HashSet operations:

```text
O(1)
```

Overall:

```text
O(N)
```

---

# Space Complexity

### HashSet Approach

```text
O(K)
```

Where:

```text
K = Number of distinct characters
```

Worst case:

```text
O(N)
```

---

### Boolean Array Approach

```text
O(1)
```

Fixed size array of length 26.

---

# Comparison of Approaches

| Approach | Time | Space |
|-----------|--------|--------|
| HashSet | O(N) | O(N) |
| Boolean Array | O(N) | O(1) |

---

# Pattern Recognition

```text
String Pattern
│
├── Remove Duplicate Characters ✅
├── First Non-Repeating Character
├── Longest Substring Without Repeating Characters
├── Remove Duplicate Letters
├── Group Anagrams
├── Valid Anagram
└── Character Frequency Problems
```

### Interview Trick

Whenever a problem asks:

```text
Duplicate Characters
Unique Characters
Visited Characters
Repeated Characters
```

Think:

```text
HashSet
or
Boolean Frequency Array
```

The key idea is:

```text
Keep the first occurrence
Skip all future occurrences
```
