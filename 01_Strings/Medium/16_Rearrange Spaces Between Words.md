# Rearrange Spaces Between Words

## Problem Statement

You are given a string `text` of words and spaces. Count the total number of spaces and words, then redistribute the spaces such that:
1. All spaces are distributed **as evenly as possible** between the words.
2. If the spaces cannot be divided evenly, place the extra leftover spaces **at the absolute end of the string**.
3. The original relative sequential order of the words must be perfectly preserved.

Return *the resulting string after rearranging the spaces*.

The overall runtime complexity must be strictly bounded by:

```text
O(n)
```

*(where n is the length of the string text)*

---

## Examples

### Example 1

**Input**

```java
text = "  this   is  a sentence "
```

**Output**

```java
"this   is   a   sentence"
```

**Explanation**

- Total string length = 25.
- There are 4 words: `["this", "is", "a", "sentence"]`. Total word slots - 1 = 3 gaps.
- Total spaces counted = 9.
- Distributed evenly: `9 / 3 = 3` spaces per gap. There are no leftover spaces (`9 % 3 = 0`).

---

### Example 2

**Input**

```java
text = " practice   makes   perfect"
```

**Output**

```java
"practice   makes   perfect "
```

**Explanation**

- Total spaces counted = 7. There are 3 words \(\implies\) 2 gaps.
- Distributed evenly: `7 / 2 = 3` spaces per gap. 
- Leftover spaces: `7 % 2 = 1` space. This single extra space is appended to the absolute end.

---

## Brute Force Approach

Extract words into a dynamic list, clean up whitespace strings via heavy string array filters, and concatenate tokens using manual lookup loops.

### Steps

1. Split the string by matching any arbitrary white spaces using regex: `text.trim().split("\\s+")`.
2. Count the spaces in the original string using a linear scan loop.
3. Calculate gaps and remainders using high-level conditional logic checking word count lengths.
4. Join the words back together piece-by-piece, appending space strings inside nested loops.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(n) // Allocates multiple intermediate string arrays and regex tokens
```

While linear, calling `.split("\\s+")` forces the JVM to instantiate multiple temporary substring object blocks on the heap. We can optimize this process down to a highly performant **single-pass extraction pipeline** with minimal allocation overhead.

---

# Optimal Approach: Two-Pointer Word Slicing with Remainder Math

## Key Idea

We can achieve optimal performance by breaking the problem down into three flat linear steps: counting resources, calculating slots, and constructing the output buffer.

1. **Resource Tally:** Count the exact number of spaces present in `text` using a fast single-pass scan.
2. **Word Slicing:** Extract all words from left to right using an optimized two-pointer technique to skip multi-space gaps, avoiding regular expression splits completely.
3. **Redistribution Math:** 
   - Let `wordCount` be the number of extracted words.
   - If `wordCount > 1`, the number of gaps between words is `wordCount - 1`. The even distribution weight is `spacesBetween = totalSpaces / gaps`, and the trailing remainder is `leftoverSpaces = totalSpaces % gaps`.
   - If `wordCount == 1` (a single word), no gaps exist. All spaces naturally become leftovers: `spacesBetween = 0`, `leftoverSpaces = totalSpaces`.
4. **Buffer Assembly:** Iterate through the words, appending the calculated `spacesBetween` blocks after every word except the final one. Finally, append the trailing `leftoverSpaces` to the end of a `StringBuilder`.

---

## Visual Understanding

Suppose `text = "  sky  blue "` with length 13:

- **Step 1 (Count):** Total characters = 13. Extracting words yields `["sky", "blue"]`. Total letter characters = 3 + 4 = 7. Therefore, `totalSpaces = 13 - 7 = 6`.
- **Step 2 (Math):** `wordCount = 2 \implies gaps = 1`.
  - `spacesBetween = 6 / 1 = 6`.
  - `leftoverSpaces = 6 % 1 = 0`.
- **Step 3 (Assemble):** Append `"sky"` \(\implies\) Append 6 spaces \(\implies\) Append `"blue"` \(\implies\) Append 0 leftover spaces.

Output sequence matches `"sky      blue"` cleanly.

---

## Partition Variables

Let:

```java
List<String> words = new ArrayList<>();
int totalSpaces = 0;
StringBuilder sb = new StringBuilder();
```

---

### Border Elements

The redistribution formulas explicitly intercept single-word boundary scenarios to shield the execution path from arithmetic `/ by zero` exceptions:

```java
int gaps = words.size() - 1;
int spacesBetween = (gaps > 0) ? totalSpaces / gaps : 0;
int leftoverSpaces = (gaps > 0) ? totalSpaces % gaps : totalSpaces;
```

---

## Correct Partition Condition

The space generation routine fills up spacing blocks cleanly using a fast, optimized loop string-builder layout:

```java
for (int i = 0; i < words.size(); i++) {
    sb.append(words.get(i));
    if (i < words.size() - 1) {
        for (int s = 0; s < spacesBetween; s++) sb.append(' ');
    }
}
for (int s = 0; s < leftoverSpaces; s++) sb.append(' ');
```

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.List;

class Solution {

    public String reorderSpaces(String text) {
        if (text == null || text.length() == 0) {
            return text;
        }

        int totalSpaces = 0;
        int n = text.length();
        List<String> words = new ArrayList<>();

        // Step 1: Count total spaces and extract words using a fast linear scan
        int i = 0;
        while (i < n) {
            char c = text.charAt(i);

            if (c == ' ') {
                totalSpaces++;
                i++;
            } else {
                // Word start boundary located
                int start = i;
                while (i < n && text.charAt(i) != ' ') {
                    i++;
                }
                // Slice and collect the isolated word token
                words.add(text.substring(start, i));
            }
        }

        // Step 2: Compute even space distribution and trailing remainder blocks
        int wordCount = words.size();
        int gaps = wordCount - 1;
        
        // Handle single-word boundary limits safely to protect from division by zero errors
        int spacesBetween = (gaps > 0) ? totalSpaces / gaps : 0;
        int leftoverSpaces = (gaps > 0) ? totalSpaces % gaps : totalSpaces;

        StringBuilder sb = new StringBuilder();

        // Step 3: Reconstruct the string from left to right
        for (int wordIdx = 0; wordIdx < wordCount; wordIdx++) {
            sb.append(words.get(wordIdx));

            // Append evenly distributed spaces if we are between words
            if (wordIdx < wordCount - 1) {
                for (int s = 0; s < spacesBetween; s++) {
                    sb.append(' ');
                }
            }
        }

        // Step 4: Flush all remaining leftover spaces straight to the absolute end
        for (int s = 0; s < leftoverSpaces; s++) {
            sb.append(' ');
        }

        return sb.toString();
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Two-Pointer Extraction** | **O(n)** | **O(n)** | Traverses characters sequentially in a single pass. The `StringBuilder` and list cache memories scale linearly with the character dimensions of the text. |

---

## Key Insight

Isolating words via manual index increment loops instead of high-level regex libraries trims intermediate garbage collection, calculating space distributions in true linear time.

```text
Time  : O(n) single pass forward execution
Space : O(n) dynamic string output builder allocation
```

---

## Similar Problems

1. Text Justification (68)
2. Length of Last Word (58)
3. Reverse Words in a String (151)
4. Number of Segments in a String (434)
5. License Key Formatting (482)
