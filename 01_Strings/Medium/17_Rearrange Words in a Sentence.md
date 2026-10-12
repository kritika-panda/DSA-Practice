# Rearrange Words in a Sentence

## Problem Statement

Given a string `text` representing a sentence, the words in it are separated by a single space and the first letter of the sentence is in uppercase. 

Rearrange the words in the sentence such that they are sorted in **ascending order by their lengths**. If two words have the same length, arrange them in their **original relative order**.

After rearranging the words, format the sentence such that:
1. The first letter is capitalized.
2. The remaining characters are in lowercase.
3. The words are separated by a single space.

Return *the resulting string after rearranging the words*.

The overall runtime complexity must be bounded by:

```text
O(n * log(n))
```

*(where n is the total number of characters in the string text)*

---

## Examples

### Example 1

**Input**

```java
text = "Leetcode is cool"
```

**Output**

```java
"Is cool leetcode"
```

**Explanation**

- `"Is"` (length 2)
- `"cool"` (length 4)
- `"leetcode"` (length 8)
Note that the first letter becomes capitalized and the rest is in lowercase.

---

### Example 2

**Input**

```java
text = "Keep calm and code on"
```

**Output**

```java
"And on code keep calm"
```

**Explanation**

Words sorted strictly by length:
- `"on"` (length 2), `"and"` (length 3), `"code"` (length 4), `"keep"` (length 4), `"calm"` (length 4).
- Keeping the original relative order for length ties: `"keep"`, `"calm"`, `"code"` all have length 4, but their original order was `"Keep" -> "calm" -> "code"`.

---

## Brute Force Approach

Split the sentence into an array of strings, lowercase the entire dataset, sort the list using a standard sorting algorithm that might not be stable, and manually re-capitalize the first element.

### Steps

1. Transform the input string completely to lowercase.
2. Split the string by matching spaces: `text.split(" ")`.
3. Sort the array using an unstable sort algorithm.
4. Capitalize the first letter of the first word.
5. Join the words back with spaces.

### Complexity

```text
Time Complexity: O(n * log(n))
Space Complexity: O(n)
```

If we use an **unstable** sorting algorithm (like traditional QuickSort on primitive objects), words with identical lengths will lose their initial relative positions, violating the problem's stability rule. We must use a **stable sorting algorithm** (like TimSort or MergeSort).

---

# Optimal Approach: Stable Sorting via Custom Length Comparator

## Key Idea

Java's built-in `Arrays.sort()` or `List.sort()` on object references uses **TimSort**, which is guaranteed to be **stable**. This means if two words have the exact same length, their original relative order will be perfectly preserved automatically without tracking their initial indices manually.

1. **Clean and Split:** Convert the entire string to lowercase first, then split it by spaces to isolate individual word tokens into an array.
2. **Stable Sorting:** Sort the word array using a custom `Comparator` that compares strings strictly by their lengths:
   ```java
   Arrays.sort(words, (a, b) -> Integer.compare(a.length(), b.length()));
   ```
3. **Re-Capitalization:** Re-capitalize the very first character of the word at index `0` to meet the sentence formatting criteria:
   ```java
   String firstWord = words;
   words = Character.toUpperCase(firstWord.charAt(0)) + firstWord.substring(1);
   ```
4. **Reconstruct:** Join the array back together with a single space delimiter using `String.join(" ", words)` or a `StringBuilder`.

---

## Visual Understanding

Suppose `text = "Leetcode is cool"`:

- **Step 1 (Split & Lowercase):** `["leetcode", "is", "cool"]`
- **Step 2 (Stable Sort by Length):**
  - `"is"` (length 2) comes first.
  - `"cool"` (length 4) comes next.
  - `"leetcode"` (length 8) comes last.
  - Sorted Array: `["is", "cool", "leetcode"]`
- **Step 3 (Re-Capitalize First Element):** Convert `"is"` to `"Is"`. Array updates to `["Is", "cool", "leetcode"]`.
- **Step 4 (Join):** `"Is cool leetcode"`

The relative ties are preserved natively while formatting stays intact.

---

## Partition Variables

Let:

```java
String[] words = text.toLowerCase().split(" ");
StringBuilder sb = new StringBuilder();
```

---

### Border Elements

The algorithm handles single-word or very short sentence boundaries flawlessly without throwing any array indexing errors:

```java
if (text == null || text.length() == 0) return "";
```

---

## Correct Partition Condition

The custom length comparison rules drive the stable sorting execution cleanly:

```java
Arrays.sort(words, (a, b) -> Integer.compare(a.length(), b.length()));
```

---

## Java Solution

```java
import java.util.Arrays;

class Solution {

    public String arrangeWords(String text) {
        if (text == null || text.length() == 0) {
            return "";
        }

        // Step 1: Lowercase the entire string and split into an array of words
        String[] words = text.toLowerCase().split(" ");

        // Step 2: Perform a stable sort by string lengths
        // Java's reference TimSort guarantees preservation of tie-breaker elements natively
        Arrays.sort(words, (a, b) -> Integer.compare(a.length(), b.length()));

        // Step 3: Capitalize the first letter of the first word
        if (words.length > 0 && words.length() > 0) {
            String firstWord = words;
            words = Character.toUpperCase(firstWord.charAt(0)) + firstWord.substring(1);
        }

        // Step 4: Join the words back together with a single space separator
        return String.join(" ", words);
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Stable Length Sort** | **O(n * log(n))** | **O(n)** | Splitting and joining strings takes linear O(n) time. TimSort processes word reference movements bounded by log-linear complexity benchmarks. |

---

## Key Insight

Leveraging the native stability property of reference object sorting libraries allows word-length variations to be managed cleanly, leaving relative position layouts intact without manual index mapping overhead.

```text
Time  : O(n * log(n)) sorting performance track
Space : O(n) token array and output allocations
```

---

## Similar Problems

1. Sort Characters By Frequency (451)
2. Reverse Words in a String (151)
3. Length of Last Word (58)
4. Sort an Array (912)
5. Merge Sorted Array (88)
