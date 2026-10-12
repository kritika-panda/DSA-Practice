# String Compression

## Problem Statement

Given an array of characters `chars`, compress it using the following algorithm:

Begin with an empty string `s`. For each group of **consecutive repeating characters** in `chars`:
* If the group's length is `1`, append the character to `s`.
* Otherwise, append the character followed by the group's length.

The compressed string `s` **should not be returned separately**. Instead, the input character array `chars` must be modified **in-place** to store the compressed result. Any remaining elements past the length of the compressed string should be ignored.

Return *the new length of the array after applying the compression algorithm*.

The overall runtime complexity must be:

```text
O(n)
```

And the space complexity must be strictly bounded by:

```text
O(1)
```

*(where n is the length of the character array chars)*

---

## Examples

### Example 1

**Input**

```java
chars = ['a', 'a', 'b', 'b', 'c', 'c', 'c']
```

**Output**

```java
Return 6, and the first 6 characters of the input array should be: ['a', '2', 'b', '2', 'c', '3']
```

**Explanation**

The groups are `"aa"`, `"bb"`, and `"ccc"`. This compresses down to `"a2b2c3"`.

---

### Example 2

**Input**

```java
chars = ['a']
```

**Output**

```java
Return 1, and the first character of the input array should be: ['a']
```

**Explanation**

The only group is `"a"`, which remains uncompressed because its length is 1.

---

### Example 3

**Input**

```java
chars = ['a', 'b', 'b', 'b', 'b', 'b', 'b', 'b', 'b', 'b', 'b', 'b', 'b']
```

**Output**

```java
Return 4, and the first 4 characters of the input array should be: ['a', 'b', '1', '2']
```

**Explanation**

The groups are `"a"` and `"bbbbbbbbbbbb"`. This compresses down to `"ab12"`.

---

## Brute Force Approach

Extract the compressed segments into a temporary string or list by reading frequencies forward, and then overwrite the original array from index 0.

### Steps

1. Create a `StringBuilder` or dynamic list to capture the characters and frequency counts.
2. Scan the character array from left to right to tally the lengths of consecutive character blocks.
3. Append characters and their digits (if count > 1) to the buffer.
4. Copy the characters from the buffer back into the original `chars` array sequentially.
5. Return the size of the buffer.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(n) // Fails the strict O(1) in-place memory constraint
```

Allocating an external string builder or array buffer breaches the constant space boundary rule. To satisfy the optimal space requirement, we must overwrite character values **in-place** using a separate modification pointer.

---

# Optimal Approach: Two-Pointer Read-Write In-Place Compression

## Key Idea

We can compress the array completely in-place in **O(n) time and O(1) space** by utilizing a **Two-Pointer technique**, managing independent `read` and `write` head coordinates.

1. Maintain a `write` pointer initialized to `0` to mark where the compressed characters will be inserted.
2. Use an anchor pointer `i = 0` to slide through the array to find consecutive sequences.
3. **The Expansion Run:** For each unique character at position `i`, start a secondary pointer `j = i`. Advance `j` forward as long as `j < chars.length` and `chars[j] == chars[i]`.
4. **The Update Action:** 
   - Write the current character to the `write` pointer location: `chars[write++] = chars[i]`.
   - Calculate the sequence length: `count = j - i`.
   - If `count > 1`, convert the integer `count` to a character sequence (to handle multidigit counts like `12`) and write each digit into the array one by one, advancing `write` at each step.
5. Move the anchor pointer forward to the next new character block: `i = j`, and repeat.

---

## Visual Understanding

Suppose `chars = ['a', 'b', 'b', 'b', 'b', 'b', 'b', 'b', 'b', 'b', 'b', 'b', 'b']`:

- **Initialization:** `write = 0`, `i = 0`.
- **Run 1 (Character 'a'):** 
  - `j` scans forward and stops at `1` because `chars[1] != 'a'`. `count = 1 - 0 = 1`.
  - Overwrite character: `chars[0] = 'a'`. `write` becomes 1. `count == 1`, so digit appending is skipped. 
  - Update pointer: `i = 1`.
- **Run 2 (Character 'b'):**
  - `j` scans forward to the end of the array. `j = 13`. `count = 13 - 1 = 12`.
  - Overwrite character: `chars[1] = 'b'`. `write` becomes 2.
  - Append digits: `count (12) > 1`. Convert `12` to characters `['1', '2']`. 
    - `chars[2] = '1'` \(\implies\) `write = 3`.
    - `chars[3] = '2'` \(\implies\) `write = 4`.
  - Update pointer: `i = 13`.

**Termination:** Loop finishes since `i == 13`. Returns `write = 4`. The array has been modified in-place to `['a', 'b', '1', '2']`.

---

## Partition Variables

Let:

```java
int write = 0;
int i = 0;
```

---

### Border Elements

Converting multidigit frequency counts to strings handles large sequences up to array bounds safely, writing digits sequentially via structural index jumps:

```java
if (count > 1) {
    for (char c : Integer.toString(count).toCharArray()) {
        chars[write++] = c;
    }
}
```

---

## Correct Partition Condition

The outer sliding loop steps directly across the unique character anchor frames in a single forward pass:

```java
while (i < chars.length) {
    int j = i;
    while (j < chars.length && chars[j] == chars[i]) {
        j++;
    }
    // ... write character and count elements ...
    i = j; // Jump ahead to the next block frame
}
```

---

## Java Solution

```java
class Solution {

    public int compress(char[] chars) {
        if (chars == null || chars.length == 0) {
            return 0;
        }

        int write = 0; // Tracks the location to write the compressed result
        int i = 0;     // Anchors the start of the current character group

        // Step 1: Scan the array from left to right
        while (i < chars.length) {
            int j = i;

            // Slide pointer j forward to find the end of the consecutive repeating run
            while (j < chars.length && chars[j] == chars[i]) {
                j++;
            }

            // Step 2: Write the current character to the write head position
            chars[write++] = chars[i];

            int count = j - i; // Calculate group length

            // Step 3: If the count is greater than 1, write its digits sequentially
            if (count > 1) {
                // Converting to string simplifies handling for multi-digit counts (e.g., 12 -> '1', '2')
                for (char c : Integer.toString(count).toCharArray()) {
                    chars[write++] = c;
                }
            }

            // Step 4: Advance the anchor pointer to the next unique character segment
            i = j;
        }

        // The write pointer value corresponds to the new length of the compressed array
        return write;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Two-Pointer Scan** | **O(n)** | **O(1)** | The `read` pointer (`j`) slides through the array elements exactly once. Modifications take place strictly in-place inside local registers with absolute zero extra space allocation. |

---

## Key Insight

Decoupling the calculation of character frequencies from the output tracking path via dual read/write pointers prevents data corruption, editing memory bounds inside an absolute constant auxiliary footprint.

```text
Time  : O(n) linear single pass sweep
Space : O(1) absolute constant extra memory footprint
```

---

## Similar Problems

1. Run-Length Encoding of a String
2. Reverse String (344)
3. Decompress Run-Length Encoded List (1313)
4. Count and Say (38)
5. Rearrange Spaces Between Words (1592)
