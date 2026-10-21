# Word Pattern

## Problem Statement

Given a `pattern` string and a string `s`, determine if `s` follows the same pattern.

Here, **follow** means a full match, such that there is a bijection (a strict one-to-one mapping) between a letter in `pattern` and a non-empty word in `s`. Specifically:
1. Each character in `pattern` must map to exactly one unique word in `s`.
2. Each unique word in `s` must map to exactly one character in `pattern`.
3. The relative sequential layout must be perfectly preserved.

The overall runtime complexity must be:

```text
O(n + m)
```

*(where n is the length of pattern and m is the length of string s)*

---

## Examples

### Example 1

**Input**

```java
pattern = "abba"
s = "dog cat cat dog"
```

**Output**

```java
true
```

**Explanation**

We can establish a perfect bijection: `'a' -> "dog"` and `'b' -> "cat"`.

---

### Example 2

**Input**

```java
pattern = "abba"
s = "dog cat cat fish"
```

**Output**

```java
false
```

**Explanation**

The trailing `'a'` maps to `"fish"`, which violates the initial `'a' -> "dog"` contract.

---

### Example 3

**Input**

```java
pattern = "aaaa"
s = "dog cat cat dog"
```

**Output**

```java
false
```

**Explanation**

The character `'a'` attempts to map to both `"dog"` and `"cat"`, which is an invalid mapping pattern.

---

## Brute Force Approach

Split the string into an array of words, then use nested loops to compare every unique character's corresponding word against all other positions down the line.

### Steps

1. Split string `s` by space tokens into a `words` array.
2. If `pattern.length() != words.length`, return `false`.
3. Run an outer loop `i` from `0` to `n - 1`.
4. Run an inner loop `j` from `i + 1` to `n - 1`.
5. If `pattern.charAt(i) == pattern.charAt(j)` but `!words[i].equals(words[j])`, return `false`.
6. If `pattern.charAt(i) != pattern.charAt(j)` but `words[i].equals(words[j])`, return `false`.

### Complexity

```text
Time Complexity: O(n^2)
Space Complexity: O(m) to hold the parsed split words array
```

This nested comparison structure scales quadratically, making it inefficient for long patterns. We can optimize this process to a single linear pass using a unified index-tracking map.

---

# Optimal Approach: Single-Pass Dual Index Tracking

## Key Idea

To confirm a strict **one-to-one mapping (bijection)** between the pattern characters and words without building two separate cross-reference maps, we can record the **last seen position** of each element.

If we map both the character and the word to the **same value** (their current 1-based index position), any structural mismatch will instantly reveal itself if they were seen at different points in history:
1. Split the string `s` into a primitive array of words using a space delimiter.
2. Fast-fail if the count of characters in `pattern` does not exactly match the count of words in the `words` array.
3. Initialize a single `HashMap<Object, Integer>` that tracks the last seen index position for both the character and the word.
4. Iterate from index `0` up to `n - 1`. For each step, invoke `map.put(key, value)`. 
5. **The Map Contract:** The `.put()` method in Java returns the **previous value** associated with the key, or `null` if the key is seen for the very first time. If the value returned for the character mismatches the value returned for the word, the pattern contract has broken down. Return `false` immediately.

---

## Visual Understanding

Suppose `pattern = "abba"` and `s = "dog cat cat fish"`:

- **Words Array:** `["dog", "cat", "cat", "fish"]`. Pointers aligned: `n == 4`.
- **i = 0 (`'a'` vs `"dog"`):**
  - `map.put('a', 1)` returns `null`. `map.put("dog", 1)` returns `null`. Both match!
- **i = 1 (`'b'` vs `"cat"`):**
  - `map.put('b', 2)` returns `null`. `map.put("cat", 2)` returns `null`. Both match!
- **i = 2 (`'b'` vs `"cat"`):**
  - `map.put('b', 3)` returns `2` (the previous index value). `map.put("cat", 3)` returns `2`. Both match!
- **i = 3 (`'a'` vs `"fish"`):**
  - `map.put('a', 4)` returns `1` (from index 0). `map.put("fish", 4)` returns `null`.
  - Mismatch intercepted! `1 != null`. The sequence breaks. Return `false`.

The structure tracks anomalies instantly within a single object map pass.

---

## Partition Variables

Let:

```java
String[] words = s.split(" ");
Map<Object, Integer> map = new HashMap<>();
```

---

### Border Elements

The length validation checks bounds early to ensure that unaligned index ranges are caught before processing keys:

```java
if (pattern.length() != words.length) {
    return false;
}
```

---

## Correct Partition Condition

Because Java handles object caching for integers natively up to 128, explicitly verify object evaluations using `.equals()` or directly unbox value comparison steps to bypass reference object comparison traps:

```java
if (!Objects.equals(map.put(pattern.charAt(i), i), map.put(words[i], i))) {
    return false; // Mismatched history footprint means structural pattern failure
}
```

---

## Java Solution

```java
class Solution {
    public boolean wordPattern(String pattern, String s) {
        String[] words = s.split(" ");
        if (words.length != pattern.length()) 
                return false;
        Map<Character, String> map = new HashMap<>();
        Set<String> usedWords = new HashSet<>();
        for (int i = 0; i < pattern.length(); i++) 
        {
            char c = pattern.charAt(i);
            String w = words[i];
            if (!map.containsKey(c)) 
            {
                if (usedWords.contains(w)) 
                    return false; 
                map.put(c, w);
                usedWords.add(w);
            } 
            else 
            {
                if (!map.get(c).equals(w)) 
                    return false; 
            }
        }
        return true;
    }
}
```

---

## Complexity Analysis

### Time Complexity: O(m)

Where:
- `m` = total number of characters in the input string `s`

#### Breakdown

##### 1. Splitting the String
```java
String[] words = s.split(" ");
```
- Scans the entire input string.
- Time Complexity: **O(m)**

##### 2. Iterating Through Pattern and Words
```java
for (int i = 0; i < pattern.length(); i++)
```
For each iteration:

```java
map.containsKey(c);      // O(1)
usedWords.contains(w);  // O(1)
map.put(c, w);          // O(1)
usedWords.add(w);       // O(1)
map.get(c);             // O(1)
```

String comparison:

```java
map.get(c).equals(w);
```

- Worst case: **O(k)**, where `k` is the word length.

Across all iterations, the total number of characters involved in comparisons is bounded by `m`.

#### Overall Time Complexity

```text
O(m) + O(m) = O(m)
```

**Time Complexity = O(m)**

> If `n` represents the number of words, interviewers often simplify this to **O(n)** assuming average O(1) hash operations.

---

### Space Complexity: O(n)

Where:
- `n` = number of words in `s`

#### 1. Word Array
```java
String[] words = s.split(" ");
```
Stores all word references.

```text
Space = O(n)
```

#### 2. HashMap
```java
Map<Character, String> map
```

Stores at most one mapping per pattern character.

```text
Space = O(n)
```

#### 3. HashSet
```java
Set<String> usedWords
```

Stores all unique words.

```text
Space = O(n)
```

#### Overall Space Complexity

```text
O(n) + O(n) + O(n) = O(n)
```

**Space Complexity = O(n)**

---

## Final Complexity

```text
Time Complexity  : O(m)
Space Complexity : O(n)
```

Where:
- `m` = total number of characters in the input string `s`
- `n` = total number of words (or `pattern.length()`)

## Similar Problems

1. Isomorphic Strings (205)
2. Valid Anagram (242)
3. Group Anagrams (49)
4. Find All Anagrams in a String (438)
5. Design Search Autocomplete System (642)
