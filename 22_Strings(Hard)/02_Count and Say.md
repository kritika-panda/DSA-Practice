# Count and Say

## Problem Statement

The **count-and-say** sequence is a sequence of digit strings as defined by the recursive formula:
- `countAndSay(1) = "1"`
- `countAndSay(n)` is the run-length encoding of `countAndSay(n - 1)`.

**Run-length encoding (RLE)** is a string compression method where consecutive identical characters are replaced by the concatenation of the character count and the character itself. For example, to compress the string `"3322251"`, we group consecutive identical characters:
- `"33"` becomes `"23"` (two `3`s)
- `"222"` becomes `"32"` (three `2`s)
- `"5"` becomes `"15"` (one `5`)
- `"1"` becomes `"11"` (one `1`)

The compressed string becomes `"23321511"`.

Given a positive integer `n`, return the `n`-th term of the count-and-say sequence.

The overall run time complexity should be:

```text
O(2^n)
```

*(since the length of the string roughly doubles at each recursive step)*

---

## Examples

### Example 1

**Input**

```java
n = 1
```

**Output**

```java
"1"
```

**Explanation**

This is the base case defined by the formula rules.

### Example 2

**Input**

```java
n = 4
```

**Output**

```java
"1211"
```

**Explanation**

- `countAndSay(1) = "1"`
- `countAndSay(2)` = say `countAndSay(1)` = say `"1"` = one `1` = `"11"`
- `countAndSay(3)` = say `countAndSay(2)` = say `"11"` = two `1`s = `"21"`
- `countAndSay(4)` = say `countAndSay(3)` = say `"21"` = one `2` followed by one `1` = `"1211"`

---

### Example 3

**Input**

```java
n = 5
```

**Output**

```java
"111221"
```

**Explanation**

Say `"1211"` = one `1`, one `2`, two `1`s = `"111221"`.

---

## Brute Force Approach

Generate strings recursively without optimizing string concatenations, copying strings repeatedly down the depth track.

### Steps

1. Check if `n == 1`. If so, return `"1"`.
2. Recursively find the string for `n - 1`.
3. Iterate through the retrieved string, using nested indexing logic to slice out character blocks, computing lengths, and creating fresh string mutations.
4. Return the resulting compiled text.

### Complexity

```text
Time Complexity: O(2^n) 
Space Complexity: O(2^n) due to heavy intermediate immutable String copying allocations
```

Using standard string object concatenation inside long loop expansions creates massive memory overhead because string objects are immutable. We can optimize this by performing linear scans using an efficient, mutable `StringBuilder` framework.

---

# Optimal Approach: Linear Run-Length Encoding Pass

## Key Idea

We can compute the sequence iteratively up to `n` steps, building each state directly from the previous string using a **two-pointer linear scan**.

1. Start with the base string `"1"` at level 1.
2. Run a loop from `2` up to `n` to progressively construct subsequent string tokens.
3. To compute the next string layer:
   - Initialize a fast, mutable `StringBuilder`.
   - Maintain a pointer `i` at index 0 and a nested pointer `j` to count consecutive matching duplicates.
   - Increment `j` while `s.charAt(i) == s.charAt(j)`. The frequency count of this block is exactly `j - i`.
   - Append `j - i` (the count) followed by `s.charAt(i)` (the character) to our builder.
   - Advance our main scan index `i` straight to `j` to process the next distinct block.

This ensures each character string layer is processed in a single, predictable forward sweep.

---

## Visual Understanding

Suppose we are constructing the string for level 4 from the level 3 string `s = "21"`:

- **Initial State:** `i = 0`, `StringBuilder sb = ""`.
- **Scan Block 1:** 
  - `s.charAt(0) = '2'`. Pointer `j` moves forward until characters mismatch.
  - At index 1, `s.charAt(1) = '1'`. Loop breaks. `j = 1`.
  - Count = `1 - 0 = 1`. Character = `'2'`. Append to builder -> `sb = "12"`.
  - Advance pointer: `i = j = 1`.
- **Scan Block 2:**
  - `s.charAt(1) = '1'`. Pointer `j` moves to index 2 (end of string). Loop breaks. `j = 2`.
  - Count = `2 - 1 = 1`. Character = `'1'`. Append to builder -> `sb = "1211"`.
  - Advance pointer: `i = j = 2`. Loop terminates.

The level 4 output `"1211"` is built cleanly in a single pass.

---

## Partition Variables

Let:

```java
String s = "1";
StringBuilder sb = new StringBuilder();
int i = 0;
```

---

### Border Elements

The scanning boundaries stop pointer `j` precisely when it reaches the edge limit of the current base string:

```java
while (j < s.length() && s.charAt(j) == s.charAt(i)) {
    j++;
}
```

---

## Correct Partition Condition

The main traversal pointer resets to the next unvisited block once the RLE block is recorded:

```java
sb.append(j - i).append(s.charAt(i));
i = j;
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitions with an iterative run-length encoding simulation pipeline to construct sequence levels in a single forward pass).*

---

## Java Solution

```java
class Solution {

    public String countAndSay(int n) {
        if (n <= 0) {
            return "";
        }

        // Base case: the 1st term is always "1"
        String s = "1";

        // Iteratively build up to the n-th term
        for (int level = 2; level <= n; level++) {
            StringBuilder sb = new StringBuilder();
            int i = 0;

            // Single pass run-length encoding scan over the current string
            while (i < s.length()) {
                int j = i;

                // Move j forward to count identical consecutive characters
                while (j < s.length() && s.charAt(j) == s.charAt(i)) {
                    j++;
                }

                // Append the count (j - i) followed by the character itself
                sb.append(j - i).append(s.charAt(i));

                // Advance i to the next new character position block
                i = j;
            }

            s = sb.toString(); // Update string snapshot for the next iteration
        }

        return s;
    }
}
```

---

## Dry Run

### Input

```java
n = 3
```

---

### Step Execution Traversal

- **Level 1:** `s = "1"`.
- **Level 2 (`level = 2`):** `s = "1"`.
  - `i = 0`. `j` increments to `1` because `s.charAt(0) == '1'`.
  - `sb.append(1 - 0).append('1')` \(\implies\) `sb = "11"`. `i = 1`. Loop ends.
  - Updates `s = "11"`.
- **Level 3 (`level = 3`):** `s = "11"`.
  - `i = 0`. `j` increments to `2` because both indices 0 and 1 are `'1'`.
  - `sb.append(2 - 0).append('1')` \(\implies\) `sb = "21"`. `i = 2`. Loop ends.
  - Updates `s = "21"`.

Loop terminates since `level` has reached `n`. Returns `"21"`.

---

### Answer

```java
"21"
```

---

## Why Is the Time Complexity Exponential?

The time complexity is bounded by the total number of characters generated across all sequence states. Although each individual string layer is processed in a strict single-pass linear sweep, the length of the string grows exponentially, growing by an approximate ratio of ≈ 1.3 at each successive step.

This sequence expansion yields:

```text
O(2^n) worst-case time complexity
```

which satisfies the sequence constraint perfectly.

---

## Complexity Analysis

### Time Complexity

```text
O(2^n)
```

Where `n` is the targeted sequence number. The lookup runtime is proportional to the cumulative lengths of all generated strings up to level `n`.

---

### Space Complexity

```text
O(2^n)
```

The `StringBuilder` allocates memory blocks dynamically to store the character lengths of the expanding sequence level segments.

---

## Key Insight

Using a two-pointer scan allows us to group and count consecutive characters in a single forward pass, while a mutable `StringBuilder` avoids the expensive object duplication overhead of standard string concatenations.

```text
Time  : O(2^n) runtime timeline
Space : O(2^n) memory tracking capacity
```

---

## Similar Problems

1. String Compression (443)
2. Run Length Encoding implementation
3. Decode String (394)
4. Continuous Subarray Sum (523)
5. Group Anagrams (49)
