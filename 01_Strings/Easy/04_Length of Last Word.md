# Length of Last Word

## Problem Statement

Given a string `s` consisting of words and spaces, return *the length of the **last** word in the string.*

A **word** is a maximal substring consisting of non-space characters only.

The overall runtime complexity must be:

```text
O(n)
```

*(where n is the length of the string s)*

---

## Examples

### Example 1

**Input**

```java
s = "Hello World"
```

**Output**

```java
5
```

**Explanation**

The last word is `"World"` with length 5.

---

### Example 2

**Input**

```java
s = "   fly me   to   the moon  "
```

**Output**

```java
4
```

**Explanation**

The last word is `"moon"` with length 4.

---

### Example 3

**Input**

```java
s = "luffy is still joyboy"
```

**Output**

```java
6
```

**Explanation**

The last word is `"joyboy"` with length 6.

---

## Brute Force Approach

Split the string into an array of sub-tokens using spaces as delimiters, filter out any empty array cells, and read the length of the final element.

### Steps

1. Trim leading and trailing spaces from the string.
2. Split the string into a collection of words using a space regular expression delimiter: `s.split(" ")`.
3. Locate the final string element in the resulting array.
4. Return its length.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(n) // Allocates array memory proportional to the string length
```

While this linear pass works, calling `.split()` inside memory-constrained loops forces the engine to instantiate multiple temporary substring object blocks. We can optimize this down to an absolute **zero-allocation footprint** by scanning the string backward.

---

# Optimal Approach: Backward Linear Scan

## Key Idea

Instead of evaluating the string from left to right, we can scan the string **backward from the end** to isolate the final word directly. This allows us to ignore all preceding text blocks completely.

1. Initialize a `length` counter to `0`.
2. Start an index pointer `i` at the final character position of the string: `s.length() - 1`.
3. **Phase 1 (Skip Trailing Spaces):** Run a loop moving `i` backward as long as the current character is a space (`' '`) and `length == 0`. This skips past any trailing spaces at the end of the text.
4. **Phase 2 (Count Word Characters):** Once a non-space character is hit, increment `length` for every consecutive character that is not a space. 
5. **Termination:** The moment a space character is encountered *after* the word tracking has already started (`length > 0`), the last word has been fully parsed. We break the loop and return the counter.

---

## Visual Understanding

Suppose `s = "  the moon  "` with length 12:

- **Initialization:** `length = 0`, index pointer `i = 11`.
- **i = 11 (' '):** Space character and `length == 0`. Skip. `i` moves to 10.
- **i = 10 (' '):** Space character and `length == 0`. Skip. `i` moves to 9.
- **i = 9 ('n'):** Non-space character! Increment `length` to 1. `i` moves to 8.
- **i = 8 ('o'):** Non-space character! Increment `length` to 2. `i` moves to 7.
- **i = 7 ('o'):** Non-space character! Increment `length` to 3. `i` moves to 6.
- **i = 6 ('m'):** Non-space character! Increment `length` to 4. `i` moves to 5.
- **i = 5 (' '):** Space character encountered and `length > 0` (4). The end of the last word is reached. Break loop.

Returns `4` instantly without ever reading the word `"the"`.

---

## Partition Variables

Let:

```java
int length = 0;
int i = s.length() - 1;
```

---

### Border Elements

The traversal pointer handles short strings, single-word entries, or strings with only space characters cleanly by checking index ranges securely at each step:

```java
while (i >= 0)
```

---

## Correct Partition Condition

The counting and termination rules evaluate text states efficiently within a single condition block:

```java
if (s.charAt(i) != ' ') {
    length++;
} else if (length > 0) {
    break; // Space found after parsing a word implies the word is finished
}
```

---

## Java Solution

```java
class Solution {

    public int lengthOfLastWord(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }

        int length = 0;
        
        // Step 1: Scan backward from the trailing edge of the string
        for (int i = s.length() - 1; i >= 0; i--) {
            char c = s.charAt(i);

            if (c != ' ') {
                length++; // Track characters of the last word
            } 
            // Step 2: Terminate immediately if a space is encountered after the word starts
            else if (length > 0) {
                break;
            }
        }

        return length;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Backward Pass** | **O(n)** | **O(1)** | In the worst-case scenario (e.g., a single long word), the loop checks `n` characters. It runs entirely in-place with zero memory allocations. |

---

## Key Insight

Scanning a string backward maps trailing word boundaries instantly, bypassing the memory overhead of array splits or full text string parsing.

```text
Time  : O(n) single pass backward execution
Space : O(1) absolute constant memory footprint
```

---

## Similar Problems

1. Longest Common Prefix (14)
2. Reverse Words in a String (151)
3. Valid Palindrome (125)
4. String to Integer / atoi (8)
5. Count and Say (38)
