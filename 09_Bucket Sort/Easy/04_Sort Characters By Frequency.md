# Sort Characters By Frequency

## Problem Statement

Given a string `s`, sort it in decreasing order based on the frequency of the characters. The frequency of a character is the number of times it appears in the string.

Return the sorted string. If there are multiple answers, return any of them.

The overall run time complexity should be:

```text
O(n)
```

---

## Examples

### Example 1

**Input**

```java
s = "tree"
```

**Output**

```java
"eert"
```

**Explanation**

'e' appears twice while 'r' and 't' both appear once.  
So 'e' must appear before both 'r' and 't'. Therefore "eetr" is also a valid answer.

---

### Example 2

**Input**

```java
s = "cccaaa"
```

**Output**

```java
"cccaaa"
```

**Explanation**

Both 'c' and 'a' appear three times, so "aaaccc" is also a valid answer.  
Note that "cacaca" is incorrect, as the same characters must be grouped together.

---

### Example 3

**Input**

```java
s = "Aabb"
```

**Output**

```java
"bbAa"
```

**Explanation**

"bbaA" is also a valid answer, but "Aabb" is incorrect.  
Note that 'A' and 'a' are treated as two different characters.

---

## Brute Force Approach

Count character frequencies using a hash map or an array, place the characters into a list, and sort the list in descending order based on their counts.

### Steps

1. Traverse the string and store character frequencies in a map or fixed-size array `(character -> frequency)`.
2. Convert the map entries into a list of characters or custom pair objects.
3. Sort the list using a comparator that checks frequencies in descending order.
4. Rebuild the string by appending each character repeatedly according to its frequency count.

### Complexity

```text
Time Complexity: O(n + k * log(k)) // where k is the unique characters count
Space Complexity: O(n + k)
```

While \(k\) is bounded by the alphabet size (making it technically constant time if bounded), a comparison sort scales with unique entries. We can achieve pure linear time using bucket distribution without sorting.

---

# Optimal Approach: Bucket Sort Strategy

## Key Idea

Instead of sorting the unique characters by their counts, we can use the **Bucket Sort** technique. 

Since the maximum frequency a character can achieve is bounded by the string length `n`, we can create an array of lists (buckets) where the index represents the frequency itself:

```text
Index of Bucket = Frequency of Characters
```

1. Count the frequency of each character using a frequency array or hash map.
2. Place each character into the bucket corresponding to its frequency count.
3. Iterate backward through the buckets (from frequency `n` down to `0`). For each character in the bucket, append it to a string builder exactly `index` times.

Because this avoids comparison-based sorting, it processes the elements in true linear time.

---

## Visual Understanding

Suppose:

```java
s = "tree"
```

1. **Build Frequency Map:**
   - `t -> 1`
   - `r -> 1`
   - `e -> 2`

2. **Populate Buckets Array (Indices 0 to 4):**
   - Bucket 0: `[]`
   - Bucket 1: `['t', 'r']` (frequency is 1)
   - Bucket 2: `['e']` (frequency is 2)
   - Bucket 3, 4: `[]`

3. **Traverse Buckets Backward:**
   - Scan Bucket 2 -> Append 'e' 2 times -> `"ee"`
   - Scan Bucket 1 -> Append 't' 1 time -> `"eet"`, Append 'r' 1 time -> `"eert"`

Final Result = `"eert"`.

---

## Partition Variables

Let:

```java
int[] freqMap = new int[128] // Assuming ASCII characters
List<Character>[] bucket = new List[s.length() + 1]
```

---

### Border Elements

The bucket array is initialized to hold lists where characters with identical counts aggregate together:

```java
for (int i = 0; i <= s.length(); i++) {
    bucket[i] = new ArrayList<>();
}
```

Characters are bucketed using their computed frequencies:

```java
int frequency = freqMap[c];
bucket[frequency].add((char) c);
```

---

## Correct Partition Condition

When pulling elements from right to left out of the buckets array:

```java
for (int pos = bucket.length - 1; pos >= 0; pos--) {
    if (bucket[pos] != null) {
        for (char c : bucket[pos]) {
            for (int i = 0; i < pos; i++) {
                sb.append(c);
            }
        }
    }
}
```

This ensures characters are processed strictly in decreasing frequency order.

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitioning with an explicit frequency bucket distribution layout to achieve linear runtime constraints).*

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.List;

class Solution {

    public String frequencySort(String s) {

        if (s == null || s.length() <= 2) {
            return s;
        }

        // Step 1: Count character frequencies
        int[] freqMap = new int[128]; // Can replace with a HashMap for Extended Unicode
        for (char c : s.toCharArray()) {
            freqMap[c]++;
        }

        // Step 2: Initialize frequency buckets
        List<Character>[] bucket = new List[s.length() + 1];
        for (int i = 0; i <= s.length(); i++) {
            bucket[i] = new ArrayList<>();
        }

        // Step 3: Distribute characters into matching frequency indices
        for (int i = 0; i < 128; i++) {
            if (freqMap[i] > 0) {
                bucket[freqMap[i]].add((char) i);
            }
        }

        // Step 4: Reconstruct the string from highest frequency downward
        StringBuilder sb = new StringBuilder();
        for (int pos = bucket.length - 1; pos >= 0; pos--) {
            if (!bucket[pos].isEmpty()) {
                for (char c : bucket[pos]) {
                    // Append character 'pos' number of times
                    for (int i = 0; i < pos; i++) {
                        sb.append(c);
                    }
                }
            }
        }

        return sb.toString();
    }
}
```

---

## Dry Run

### Input

```java
s = "tree"
```

---

### Initial Maps and Buckets

```java
freqMap['t'] = 1, freqMap['r'] = 1, freqMap['e'] = 2
bucket array size = 5
bucket[1] = ['t', 'r']
bucket[2] = ['e']
```

---

### Gathering Phase

```java
sb = ""
```

- **pos = 4:** Empty bucket.
- **pos = 3:** Empty bucket.
- **pos = 2:** Contains `['e']`. Append 'e' 2 times -> `sb = "ee"`.
- **pos = 1:** Contains `['t', 'r']`. Append 't' 1 time -> `sb = "eet"`. Append 'r' 1 time -> `sb = "eert"`.
- **pos = 0:** Empty bucket.

Loop terminates after checking all positions.

---

### Answer

```java
"eert"
```

---

## Why Do We Use the Bucket Sorting Principle?

The maximum count any single text token can reach cannot exceed the absolute length of the input data stream `n`. Mapping characters straight into index ranks matching their aggregate frequencies works as an exceptional non-comparison sort, completely bypassing standard sorting performance penalties.

This bucket layout yields:

```text
O(n)
```

which satisfies the optimal time constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

Building the frequency track takes `O(n)` time. Distributing active characters into buckets takes a constant `O(128)` step execution. Traversing the frequency slots backward to recreate the string handles exactly `n` total structural char iterations.

---

### Space Complexity

```text
O(n)
```

The bucket list array requires matching memory allocations to reference arrays totaling at most `n` characters, and the string builder consumes `O(n)` memory to stage the final output.

---

## Key Insight

When the maximum possible frequency of a value is naturally restricted by the volume of input records, utilizing the metric rank value directly as an index completely removes comparison sorting performance caps.

```text
Time  : O(n)
Space : O(n)
```

---

## Similar Problems

1. Top K Frequent Elements (347)
2. Sort Characters By Frequency II
3. First Unique Character in a String (387)
4. Rearrange String k Distance Apart (358)
5. Task Scheduler (621)
