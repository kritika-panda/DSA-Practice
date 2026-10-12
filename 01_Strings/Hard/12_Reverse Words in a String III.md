# Reverse Words in a String III

## Problem Statement

Given a string `s`, reverse the order of characters in each word within a sentence while still preserving whitespace and initial word order.

A **word** is defined as a sequence of non-space characters. Words are separated by a single space delimiter.

The overall runtime complexity must be:

```text
O(n)
```

*(where n is the total number of characters in the string s)*

---

## Examples

### Example 1

**Input**

```java
s = "Let's take LeetCode contest"
```

**Output**

```java
"s'teL ekat edoCteeL tsetnoc"
```

---

### Example 2

**Input**

```java
s = "Mr Ding"
```

**Output**

```java
"rM gniD"
```

---

## Brute Force Approach

Split the sentence into an array of words using built-in string libraries, loop through each individual word to invert its character sequence manually, and join them back together with spaces.

### Steps

1. Split the string by matching space delimiters: `s.split(" ")`.
2. Initialize a results `StringBuilder`.
3. Loop through the array of tokens from index `0` up to `words.length - 1`.
4. Invert each token using a fresh `StringBuilder(word).reverse()`.
5. Concat each inverted word followed by a single space, stripping the final trailing space at termination.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(n) // Allocates multiple intermediate string copies and token arrays
```

While linear asymptotically, generating full sub-token lists and temporary string mirrors forces high object churn on the heap memory. We can optimize this process down to a highly performant **in-place linear two-pointer pass** with a zero-allocation pipeline.

---

# Optimal Approach: Two-Pointer Word Anchor Search

## Key Idea

Instead of dividing the text layout into structural lists, we can process the characters inside a mutable array directly from left to right, locating the precise start and end boundaries of each word on the fly.

1. **Array Conversion:** Convert the input string into a mutable primitive `char[] arr = s.toCharArray()` to perform transformations directly in-place.
2. **Anchor Index Navigation:** Maintain an index pointer `i = 0` to scan forward.
3. While `i < arr.length`:
   - **Phase 1 (Locate Start Anchor):** Skip any unexpected spaces by advancing `i`. Once a non-space character is found, mark this as the starting boundary of a word: `start = i`.
   - **Phase 2 (Locate End Anchor):** Slide a lookahead pointer `j = i` forward until it encounters a space character `' '` or strikes the absolute end of the array. The index `j - 1` marks the terminal trailing character of the active word.
   - **Phase 3 (In-Place Inversion):** Execute a standard two-pointer swap sweep within the boundaries `[start, j - 1]`, reversing the isolated characters completely in-place.
   - **Phase 4 (Advance Step):** Fast-forward the main tracking index to the next position past the word: `i = j + 1`.

---

## Visual Understanding

Suppose `s = "Mr Ding"`:

- **Initial State:** `arr = ['M', 'r', ' ', 'D', 'i', 'n', 'g']`, pointer `i = 0`.
- **Run 1:** 
  - `start = 0`. Pointer `j` advances until hitting space at index `2`. 
  - Isolated Word Range: `[0, 1]` (`"Mr"`).
  - Reverse sub-segment: Swap `arr[0]` and `arr[1]`. Array becomes `['r', 'M', ' ', 'D', 'i', 'n', 'g']`.
  - Advance pointer: `i = 2 + 1 = 3`.
- **Run 2:**
  - `start = 3`. Pointer `j` advances to the end of the array. `j = 7`.
  - Isolated Word Range: `[3, 6]` (`"Ding"`).
  - Reverse sub-segment: Swap positions symmetrically from both ends inward (`'D' <-> 'g'`, `'i' <-> 'n'`). Array updates to `['r', 'M', ' ', 'g', 'n', 'i', 'D']`.
  - Advance pointer: `i = 7 + 1 = 8`.

**Termination:** Loop exits because `i > 7`. The modified array is wrapped back into a string instantly.

---

## Partition Variables

Let:

```java
char[] arr = s.toCharArray();
int i = 0;
```

---

### Border Elements

The swapping operations handle word ranges at the extreme edges of the array smoothly without requiring additional index padding:

```java
int left = start;
int right = j - 1;
while (left < right) {
    char temp = arr[left];
    arr[left] = arr[right];
    arr[right] = temp;
    left++;
    right--;
}
```

---

## Correct Partition Condition

The forward scanning loop steps directly across word frames using the evaluated segment lookahead boundaries:

```java
while (i < arr.length) {
    int start = i;
    while (i < arr.length && arr[i] != ' ') {
        i++;
    }
    reverse(arr, start, i - 1);
    i++; // Step past the space character
}
```

---

## Java Solution

```java
class Solution {

    public String reverseWords(String s) {
        if (s == null || s.length() <= 1) {
            return s;
        }

        // Convert string to a mutable primitive character array to process in-place
        char[] arr = s.toCharArray();
        int i = 0;
        int n = arr.length;

        // Step 1: Scan the array from left to right to locate words
        while (i < n) {
            int start = i;

            // Slide pointer forward until hitting a space or the end of the array
            while (i < n && arr[i] != ' ') {
                i++;
            }

            // Step 2: Reverse the isolated word segment in-place
            int left = start;
            int right = i - 1;
            while (left < right) {
                char temp = arr[left];
                arr[left] = arr[right];
                arr[right] = temp;
                left++;
                right--;
            }

            // Step 3: Advance past the space character to search for the next word
            i++;
        }

        // Step 4: Reconstruct the output string directly from the modified array
        return new String(arr);
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Two-Pointer Word Pass** | **O(n)** | **O(n)** | Traverses each character in the string at most twice (once for reading indices, once for swapping). Allocates a single character array matching the original string dimensions. |

---

## Key Insight

Symmetrically inverting local indices within identified word boundaries allows text fields to be transformed cleanly, bypassing the heap allocation overhead of regular expression string libraries.

```text
Time  : O(n) single pass forward execution pass
Space : O(n) primitive character array allocation
```

---

## Similar Problems

1. Reverse Words in a String (151)
2. Reverse String (344)
3. Reverse String II (541)
4. Valid Palindrome (125)
5. Rearrange Spaces Between Words (1592)
