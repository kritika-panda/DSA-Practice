# Keyboard Row

## Problem Statement

Given an array of strings `words`, return *the words that can be typed using letters of the alphabet from only **one row** of American keyboard*.

In the American keyboard, the rows of characters are specified as follows:
* **First Row:** `"qwertyuiop"`
* **Second Row:** `"asdfghjkl"`
* **Third Row:** `"zxcvbnm"`

---

## Examples

### Example 1

**Input**

```java
words = ["Hello", "Alaska", "Dad", "Peace"]
```

**Output**

```java
["Alaska", "Dad"]
```

**Explanation**

- `"Hello"` uses characters from Row 1 (`e`, `o`, `l`) and Row 2 (`h`), violating the single-row constraint.
- `"Alaska"` uses only characters from Row 2 (`a`, `l`, `s`, `k`). Keep it.
- `"Dad"` uses only characters from Row 2 (`d`, `a`). Keep it.
- `"Peace"` uses characters from Row 1 (`e`, `r`, `p`, `c`) and Row 3 (`c`), violating the constraint.

---

### Example 2

**Input**

```java
words = ["omk"]
```

**Output**

```java
[]
```

---

### Example 3

**Input**

```java
words = ["adsdf", "sdsd"]
```

**Output**

```java
["adsdf", "sdsd"]
```

---

## Brute Force Approach

For every word in the array, compare each character against every keyboard row using a collection of strings or character arrays to ensure all characters reside within a single set.

### Steps

1. Define three distinct string sets representing the keyboard rows.
2. Initialize an empty list to capture matching valid strings.
3. For each word, convert all its characters to lowercase.
4. Iterate across all characters of the word and explicitly check their presence across row 1, row 2, and row 3.
5. If characters span across more than one row dictionary, discard the word. Otherwise, add the word to the output list.

### Complexity

```text
Time Complexity: O(n * L) // where n is the total number of words and L is the length of the longest word
Space Complexity: O(n * L) to hold intermediate string clones
```

While functional, continuously querying strings or dynamic collections inside inner character loops adds unneeded structural and computational lookup overhead. We can achieve a faster solution by pre-mapping every alphabet character to its respective row ID using a single fixed-size lookup table.

---

# Optimal Approach: Precalculated Bitmask / Alphabet Map Array

## Key Idea

Instead of mapping word characters dynamically to keyboard string layouts at runtime, we can assign each character a static **Row ID** upfront using a fixed-size integer array of size 26.

1. **Precalculate the Alphabet Map:** Create an integer array `rowMap` where the index represents the alphabetical offset `c - 'a'` and the stored value indicates the keyboard row (e.g., `rowMap['q' - 'a'] = 1`, `rowMap['a' - 'a'] = 2`, etc.).
2. **Single Pass Word Filtering:** For each word in the input array:
   - Convert its first character to lowercase and fetch its corresponding Row ID from our table. Let this be our `targetRow`.
   - Iterate through the remaining characters of the word. Fetch each character's Row ID and compare it against `targetRow`.
   - **The Violation Check:** If any character yields a Row ID that mismatches `targetRow`, the word spans multiple rows. We flag it as invalid and break out early.
   - If the entire word is scanned without any row index conflicts, add it to our result list.

---

## Visual Understanding

Suppose `words = ["Dad"]`:

- **Precalculated Layout Maps:**
  - `rowMap['d' - 'a'] = 2`, `rowMap['a' - 'a'] = 2`.
- **Processing "Dad":**
  - Character 0: `'D'` \(\implies\) lowercase `'d'`. Target row identifier becomes `rowMap['d' - 'a'] = 2`.
  - Character 1: `'a'` \(\implies\) `rowMap['a' - 'a']` is `2`. `2 == 2` (Valid match).
  - Character 2: `'d'` \(\implies\) `rowMap['d' - 'a']` is `2`. `2 == 2` (Valid match).

The entire text fits Row 2 perfectly. The word is safely added to the output array list in a single pass.

---

## Partition Variables

Let:

```java
int[] rowMap = new int;
List<String> validWords = new ArrayList<>();
```

---

### Border Elements

The precalculation mapping loop maps both lowercase and uppercase offsets gracefully by building the static translation dictionary cleanly before launching the filtration pass:

```java
String[] rows = {"qwertyuiop", "asdfghjkl", "zxcvbnm"};
for (int i = 0; i < rows.length; i++) {
    for (char c : rows[i].toCharArray()) {
        rowMap[c - 'a'] = i + 1;
    }
}
```

---

## Correct Partition Condition

The linear validation check evaluates character row alignment properties instantly by calculating index offsets directly relative to the base anchor character `'a'`:

```java
int charRow = rowMap[Character.toLowerCase(word.charAt(i)) - 'a'];
if (charRow != targetRow) {
    isValid = false;
    break;
}
```

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.List;

class Solution {

    public String[] findWords(String[] words) {
        if (words == null || words.length == 0) {
            return new String[0];
        }

        // Fixed-size lookup array mapping each lowercase English letter to its keyboard row (1, 2, or 3)
        int[] rowMap = new int;
        String[] keyboardRows = {"qwertyuiop", "asdfghjkl", "zxcvbnm"};

        // Step 1: Precalculate the row mapping for all 26 letters
        for (int rowIdx = 0; rowIdx < keyboardRows.length; rowIdx++) {
            for (char c : keyboardRows[rowIdx].toCharArray()) {
                rowMap[c - 'a'] = rowIdx + 1;
            }
        }

        List<String> resultList = new ArrayList<>();

        // Step 2: Validate each word in a single pass
        for (String word : words) {
            if (word.isEmpty()) {
                continue;
            }

            // Extract the baseline row identifier from the very first character
            int targetRow = rowMap[Character.toLowerCase(word.charAt(0)) - 'a'];
            boolean isValid = true;

            // Verify if all subsequent characters reside within the same target row
            for (int i = 1; i < word.length(); i++) {
                int currentRow = rowMap[Character.toLowerCase(word.charAt(i)) - 'a'];
                
                if (currentRow != targetRow) {
                    isValid = false; // Intercept row layout conflicts instantly
                    break;
                }
            }

            if (isValid) {
                resultList.add(word);
            }
        }

        // Step 3: Convert the dynamic collection back to a standard String array
        return resultList.toArray(new String[0]);
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Alphabet Map Filtering** | **O(N * L)** | **O(1)** | Where `N` is the total number of words and `L` is the average length of a word. The alphabet mapping table allocation is strictly fixed at 26 cells, representing an absolute constant O(1) memory footprint. |

---

## Key Insight

Pre-mapping alphabetic character sets to structural row integers substitutes expensive runtime string scanning operations with fast, constant-time index address lookups, tracking keyboard configurations in optimal linear time.

```text
Time  : O(N * L) linear execution pass
Space : O(1) absolute constant auxiliary tracking memory
```

---

## Similar Problems

1. Fizz Buzz (412)
2. Valid Anagram (242)
3. Word Pattern (290)
4. First Unique Character in a String (387)
5. Detect Capital (520)
