# Reverse Words in a String

## Problem Statement

Given an input string `s`, reverse the order of the **words**.

A **word** is defined as a sequence of non-space characters. The words in `s` will be separated by at least one space. Return *a string of the words in reverse order concatenated by a single space.*

Note that `s` may contain leading or trailing spaces or multiple spaces between two words. The returned string should only have a single space separating the words. **Do not include any extra spaces.**

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
s = "the sky is blue"
```

**Output**

```java
"blue is sky the"
```

---

### Example 2

**Input**

```java
s = "  hello world  "
```

**Output**

```java
"world hello"
```

**Explanation**

Your reversed string should not contain leading or trailing spaces.

---

### Example 3

**Input**

```java
s = "a good   example"
```

**Output**

```java
"example good a"
```

**Explanation**

You need to reduce multiple spaces between two words to a single space in the reversed string.

---

## Brute Force Approach

Split the string into an array of words using built-in regular expressions, filter out the empty strings caused by multiple spaces, and then join the words backward from the end.

### Steps

1. Trim leading and trailing spaces: `s.trim()`.
2. Split the string by matching any number of spaces: `s.split("\\s+")`.
3. Initialize a new `StringBuilder`.
4. Loop through the array of tokens backward from the final index down to `0`.
5. Append each word followed by a space, then trim or strip the final trailing space before returning.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(n) // Allocates multiple intermediate string copies and token arrays
```

While linear, calling `.split("\\s+")` forces the JVM to instantiate multiple temporary substring object blocks and array structures on the heap. We can optimize this to a highly performant **single-pass backward parsing pipeline** with minimal overhead.

---

# Optimal Approach: Two-Pointer Backward Word Extraction

## Key Idea

Since the problem requires reversing the order of the words, scanning the string **backward from right to left** allows us to extract the final words first. We can build our result dynamically using a `StringBuilder` without allocating large intermediate token arrays.

1. Initialize a `StringBuilder result = new StringBuilder()`.
2. Start an index pointer `i` at the final character position of the string: `s.length() - 1`.
3. **The Extraction Loop:** While `i >= 0`:
   - **Phase 1 (Skip Spaces):** Move `i` backward while `i >= 0` and `s.charAt(i) == ' '`. This skips past trailing or interstitial space gaps.
   - If `i < 0`, all words are processed. Break the loop.
   - **Phase 2 (Locate Word Boundary):** Set a tracking pointer `right = i`. This marks the end of a valid word. Now, move `i` backward while `i >= 0` and `s.charAt(i) != ' '`. This finds the start boundary of the word.
   - **Phase 3 (Extract and Append):** Extract the word from index `i + 1` to `right + 1` using `s.substring(i + 1, right + 1)`. Append it to the `result` buffer.
   - Append a single space character `' '` to separate it from the next incoming word.
4. After the loop completes, strip the very final trailing space from the `result` buffer and return the text.

---

## Visual Understanding

Suppose `s = "  sky  blue "` with length 13:

- **Initialization:** `result = ""`, index pointer `i = 12`.
- **Iteration 1:**
  - Skip spaces: `s.charAt(12)` is `' '` \(\implies\) `i` decrements down to `10`.
  - Mark word end: `right = 10` (pointing to `'e'`).
  - Move to word start: `i` moves past `'e'`, `'u'`, `'l'`, `'b'` to index `6` (pointing to `' '`).
  - Extract word: `s.substring(7, 11)` \(\implies\) `"blue"`.
  - Append to buffer: `result = "blue "`. Pointer `i = 6`.
- **Iteration 2:**
  - Skip spaces: `s.charAt(6)` and `s.charAt(5)` are spaces \(\implies\) `i` decrements down to `4`.
  - Mark word end: `right = 4` (pointing to `'y'`).
  - Move to word start: `i` moves past `'y'`, `'k'`, `'s'` to index `1` (pointing to `' '`).
  - Extract word: `s.substring(2, 5)` \(\implies\) `"sky"`.
  - Append to buffer: `result = "blue sky "`. Pointer `i = 1`.
- **Iteration 3:** Skip spaces drives `i` to `-1`. Loop terminates.
- **Final Cleanup:** Strip trailing space \(\implies\) `"blue sky"`.

---

## Partition Variables

Let:

```java
StringBuilder result = new StringBuilder();
int i = s.length() - 1;
```

---

### Border Elements

The traversal loops safely guard boundaries at each step to prevent index exceptions on clean or heavily padded edges:

```java
while (i >= 0 && s.charAt(i) == ' ') {
    i--;
}
```

---

## Correct Partition Condition

A single space delimiter is appended dynamically after each extracted chunk, and a cleanup validation clears the final trailing space at termination:

```java
if (result.length() > 0) {
    result.setLength(result.length() - 1); // Clears the final trailing space smoothly
}
return result.toString();
```

---

## Java Solution

```java
class Solution {

    public String reverseWords(String s) {
        if (s == null || s.length() == 0) {
            return "";
        }

        StringBuilder result = new StringBuilder();
        int i = s.length() - 1;

        // Step 1: Scan the string backward from right to left
        while (i >= 0) {
            
            // Skip any spaces between or after words
            while (i >= 0 && s.charAt(i) == ' ') {
                i--;
            }

            // Break if we ran out of characters while skipping spaces
            if (i < 0) {
                break;
            }

            // Mark the trailing edge (end) of the current word
            int right = i;

            // Move pointer leftward until we hit a space to find the start of the word
            while (i >= 0 && s.charAt(i) != ' ') {
                i--;
            }

            // Extract the word slice and append it followed by a single space separator
            result.append(s.substring(i + 1, right + 1));
            result.append(' ');
        }

        // Step 2: Remove the absolute final trailing space if any words were added
        if (result.length() > 0) {
            result.setLength(result.length() - 1);
        }

        return result.toString();
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Backward Two-Pointer Pass** | **O(n)** | **O(n)** | Traverses each character in the string at most twice. The `StringBuilder` allocates memory cells matching the character size of the cleaned output text. |

---

## Key Insight

Extracting word slices backward from the trailing edge maps word order sequences directly into the result buffer, bypassing the memory and performance overhead of regular expression splits.

```text
Time  : O(n) single pass backward execution
Space : O(n) dynamic string output builder allocation
```

---

## Similar Problems

1. Length of Last Word (58)
2. Reverse String (344)
3. Reverse Vowels of a String (345)
4. Rotate String (796)
5. String Compression (443)
