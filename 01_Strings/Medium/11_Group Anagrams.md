# Group Anagrams

## Problem Statement

Given an array of strings `strs`, group the **anagrams** together. You can return the answer in **any order**.

An **Anagram** is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

The overall runtime complexity must be:

```text
O(n * L)
```

*(where n is the total number of strings in the array and L is the maximum length of a string)*

---

## Examples

### Example 1

**Input**

```java
strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
```

**Output**

```java
[["bat"], ["nat", "tan"], ["ate", "eat", "tea"]]
```

**Explanation**

- `"bat"` has no other anagrams in the list.
- `"nat"` and `"tan"` both use the letters `a`, `n`, and `t` exactly once.
- `"ate"`, `"eat"`, and `"tea"` all use the letters `a`, `e`, and `t` exactly once.

---

### Example 2

**Input**

```java
strs = [""]
```

**Output**

```java
[[""]]
```

---

### Example 3

**Input**

```java
strs = ["a"]
```

**Output**

```java
[["a"]]
```

---

## Brute Force Approach

Compare every pair of strings in the array using a character frequency array or sorting to check if they are anagrams, tracking grouped elements using a boolean visited array.

### Steps

1. Initialize a `boolean[] visited` array of size `n` to track grouped strings.
2. Run an outer loop `i` from `0` to `n - 1`. If `visited[i]` is true, skip it.
3. Create a new sub-list containing `strs[i]` and mark `visited[i] = true`.
4. Run an inner loop `j` from `i + 1` to `n - 1`. If `visited[j]` is true, skip it.
5. Check if `strs[i]` and `strs[j]` are anagrams. If they match, add `strs[j]` to the sub-list and mark `visited[j] = true`.
6. Append the sub-list to the final results container.

### Complexity

```text
Time Complexity: O(n^2 * L) // due to comparing all pairs of strings
Space Complexity: O(1) auxiliary space (excluding the output collection list)
```

Comparing all pairs of strings creates a heavy quadratic time bottleneck that causes a Time Limit Exceeded (TLE) error for large input arrays. We can optimize this by mapping words to a unique serialized key inside a `HashMap`.

---

# Optimal Approach: Frequency Array Serialization via HashMap

## Key Idea

If two strings are anagrams of each other, they share the exact same characters with the exact same frequencies. 

Instead of sorting individual character arrays—which adds an extra \(O(L \log L)\) sorting penalty per word—we can generate a unique **serialized string key** based on character counts. Since the problem operates on a finite lowercase English alphabet, we can count letter occurrences using a fixed array of size 26.

1. Initialize a `HashMap<String, List<String>> map` where the key is the unique serialized frequency string, and the value is a list of all matching anagram strings.
2. Iterate through each string in `strs`:
   - Initialize a local integer array `count` of size 26.
   - Loop through the string characters and increment their matching alphabet bucket cell: `count[c - 'a']++`.
   - Serialize the array into a standardized unique key string wrapper format (e.g., `#1#0#1...` or using a specialized char delimiter structure).
   - If the key is missing from the map, initialize a new `ArrayList`. Append the original word to the list corresponding to that serialized key.
3. Return a new `ArrayList` wrapping `map.values()`.

---

## Visual Understanding

Suppose `strs = ["eat", "tea", "tan"]`:

- **Processing `"eat"`:**
  - Frequency tracking array: `[a:1, e:1, t:1, others:0]`.
  - Serialized string key: `"1,0,0,0,1,0,...,1,0"` (abbreviated).
  - Map state: `{"1,0,0,0,1,0,...,1,0" -> ["eat"]}`.
- **Processing `"tea"`:**
  - Frequency tracking array: `[a:1, e:1, t:1, others:0]`.
  - Serialized string key: `"1,0,0,0,1,0,...,1,0"`.
  - Match hit! Append to value list: `{"1,0,0,0,1,0,...,1,0" -> ["eat", "tea"]}`.
- **Processing `"tan"`:**
  - Frequency tracking array: `[a:1, n:1, t:1, others:0]`.
  - Serialized string key: `"1,0,0,0,0,0,...,1,...,1,0"`.
  - New key added. Map state updates with independent bucket fields.

The words are classified smoothly without sorting elements.

---

## Partition Variables

Let:

```java
Map<String, List<String>> map = new HashMap<>();
int[] count = new int[26];
```

---

### Border Elements

The serialization routine uses a `StringBuilder` combined with delimiter characters to guarantee that frequencies do not bleed together unexpectedly (e.g., distinguishing 11 'a's from two 1s):

```java
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 26; i++) {
    sb.append('#').append(count[i]);
}
String key = sb.toString();
```

---

## Correct Partition Condition

The map values are fetched or initialized atomically using elegant default grouping wrappers:

```java
map.computeIfAbsent(key, k -> new ArrayList<>()).add(s);
```

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

class Solution {

    public List<List<String>> groupAnagrams(String[] strs) {
        if (strs == null || strs.length == 0) {
            return new ArrayList<>();
        }

        // Map to group original strings by their unique serialized frequency key
        Map<String, List<String>> anagramGroups = new HashMap<>();

        // Step 1: Scan each string in the input array
        for (String s : strs) {
            int[] count = new int[26];
            
            // Tally character frequencies for the current word
            for (int i = 0; i < s.length(); i++) {
                count[s.charAt(i) - 'a']++;
            }

            // Step 2: Serialize the frequency tally into a unique string key
            StringBuilder sb = new StringBuilder();
            for (int i = 0; i < 26; i++) {
                sb.append('#');
                sb.append(count[i]);
            }
            String key = sb.toString();

            // Step 3: Add the original string to its corresponding anagram group list
            anagramGroups.computeIfAbsent(key, k -> new ArrayList<>()).add(s);
        }

        // Step 4: Extract and return all values from the grouped map
        return new ArrayList<>(anagramGroups.values());
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Frequency Serialization** | **O(n * L)** | **O(n * L)** | Loops through `n` strings and reads `L` characters per string exactly once. The `HashMap` stores up to `n` unique elements containing the character payloads. |

---

## Key Insight

Mapping word frequencies onto a standardized serialized string key skips the log-linear overhead of sorting individual strings, grouping equivalent anagram sets in true linear time.

```text
Time  : O(n * L) linear execution performance
Space : O(n * L) dynamic map bucket and key structure allocations
```

---

## Similar Problems

1. Valid Anagram (242)
2. Find All Anagrams in a String (438)
3. Word Pattern (290)
4. Isomorphic Strings (205)
5. Palindrome Partitioning (131)
