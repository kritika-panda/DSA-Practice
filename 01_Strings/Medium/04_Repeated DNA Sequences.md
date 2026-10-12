# Repeated DNA Sequences

## Problem Statement

The DNA sequence is composed of a series of nucleotides abbreviated as `'A'`, `'C'`, `'G'`, and `'T'`. When studying DNA, it is useful to identify repeated sequences within the DNA.

Given a string `s` that represents a DNA sequence, return *all the **10-character-long** substrings (subsequences) that occur more than once in a DNA molecule*. You may return the answer in **any order**.

The overall runtime complexity must be strictly bounded by:

```text
O(n)
```

*(where n is the length of the string s)*

---

## Examples

### Example 1

**Input**

```java
s = "AAAAACCCCCAAAAACCCCCCAAAAAGGGTTT"
```

**Output**

```java
["AAAAACCCCC", "CCCCCAAAAA"]
```

---

### Example 2

**Input**

```java
s = "AAAAAAAAAAAAA"
```

**Output**

```java
["AAAAAAAAAA"]
```

**Explanation**

The 10-character-long substring `"AAAAAAAAAA"` occurs multiple times as overlapping windows.

---

### Example 3

**Input**

```java
s = "ACGT"
```

**Output**

```java
[]
```

---

## Brute Force Approach

Use two nested loops to extract every possible 10-character substring and compare it against all subsequent 10-character windows to count occurrences.

### Steps

1. Run an outer loop tracking index `i` from `0` to `n - 10`.
2. Extract the current window: `sub1 = s.substring(i, i + 10)`.
3. Run an inner loop tracking index `j` from `i + 1` to `n - 10`.
4. Extract the comparison window: `sub2 = s.substring(j, j + 10)`.
5. If `sub1.equals(sub2)`, record it in a uniqueness tracking container.

### Complexity

```text
Time Complexity: O(n^2) // due to nested comparison windows
Space Complexity: O(n) to cache found duplicate keys
```

Comparing overlapping string windows explicitly across a large DNA strand causes severe performance bottlenecks. We can optimize this process down to a single linear pass by using hash tracking.

---

# Optimal Approach: Sliding Window with HashSet Tracking

## Key Idea

We can solve this problem in **linear time** by sliding a fixed window of size 10 from left to right across the string `s` while caching our history using two **HashSets**:
1. `seen`: Stores every 10-character substring we encounter for the first time.
2. `repeated`: Stores substrings that are encountered a second time. This prevents us from adding the same duplicate sequence multiple times to our final output list.

As we slide the window:
- Extract the 10-character slice starting at index `i`: `sequence = s.substring(i, i + 10)`.
- Attempt to add `sequence` into the `seen` set using `seen.add(sequence)`.
- **The Duplicate Catch:** If `seen.add(sequence)` returns `false`, it means the sequence has been seen before. We then immediately add it to our `repeated` set.

*(Note: While extracting strings via `.substring()` takes O(10) = O(1) time due to a fixed length, for ultra-high performance or massive data tracks, you can convert nucleotides into a 2-bit integer system and use a bitwise rolling hash. However, standard `HashSet` string tracking satisfies the interview performance constraints perfectly).*

---

## Visual Understanding

Suppose `s = "AAAAAAAAAAAAA"` (13 characters):

- **i = 0:** Extract `s.substring(0, 10)` \(\implies\) `"AAAAAAAAAA"`. Not in `seen`. Add it. `seen = ["AAAAAAAAAA"]`.
- **i = 1:** Extract `s.substring(1, 11)` \(\implies\) `"AAAAAAAAAA"`. Already in `seen`! Add to `repeated`. `repeated = ["AAAAAAAAAA"]`.
- **i = 2:** Extract `s.substring(2, 12)` \(\implies\) `"AAAAAAAAAA"`. Already in `seen`! Add to `repeated` (Set rejects duplicates, so it stays unique).
- **i = 3:** Extract `s.substring(3, 13)` \(\implies\) `"AAAAAAAAAA"`. Already in `seen`! Add to `repeated`.

The loop finishes. `repeated` contains exactly one instance of `"AAAAAAAAAA"`.

---

## Partition Variables

Let:

```java
Set<String> seen = new HashSet<>();
Set<String> repeated = new HashSet<>();
```

---

### Border Elements

If the string's length is strictly less than 10, it cannot contain any valid target sequence. Return an empty list instantly:

```java
if (s == null || s.length() < 10) {
    return new ArrayList<>();
}
```

---

## Correct Partition Condition

The forward sliding window loop scans precisely up to the ultimate boundary limit:

```java
for (int i = 0; i <= s.length() - 10; i++) {
    String sequence = s.substring(i, i + 10);
    if (!seen.add(sequence)) {
        repeated.add(sequence);
    }
}
```

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

class Solution {

    public List<String> findRepeatedDnaSequences(String s) {
        // Fast-fail: If string is shorter than the target window size, no matches can exist
        if (s == null || s.length() < 10) {
            return new ArrayList<>();
        }

        Set<String> seen = new HashSet<>();
        Set<String> repeated = new HashSet<>();

        // Slide a window of size 10 across the entire string length
        for (int i = 0; i <= s.length() - 10; i++) {
            String sequence = s.substring(i, i + 10);

            // .add() returns false if the element already exists in the set
            if (!seen.add(sequence)) {
                repeated.add(sequence); // Found a sequence that occurs more than once
            }
        }

        // Convert the repeated set into the required list format
        return new ArrayList<>(repeated);
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Sliding Window Hash** | **O(n)** | **O(n)** | Traverses the string containing `n` elements exactly once. Since the window length is fixed at a small constant (10), string extraction and hashing operations run in O(1) time. The maps store up to `n` unique elements. |

---

## Key Insight

Using dual HashSets to distinguish between newly discovered paths and verified repetitions eliminates duplicate tracking checks, isolating repeated sub-segments in a single linear pass.

```text
Time  : O(n) linear sliding window pass
Space : O(n) dynamic tracking memory footprint
```

---

## Similar Problems

1. Longest Substring Without Repeating Characters (3)
2. Find All Anagrams in a String (438)
3. Substring with Concatenation of All Words (30)
4. Rabin-Karp Algorithm / Rolling Hash Implementation
5. Longest Duplicate Substring (1044)
