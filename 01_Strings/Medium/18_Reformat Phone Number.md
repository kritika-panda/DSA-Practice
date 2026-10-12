# Reformat Phone Number

## Problem Statement

You are given a phone number as a string `number`. `number` consists of digits, spaces `' '`, and/or dashes `'-'`.

You would like to reformat the phone number in a certain behavior. First, **remove all spaces and dashes**. Then, group the digits from left to right into blocks of length 3 until there are 4 or fewer digits left. The final digits are then grouped as follows:
* **2 digits remaining:** A single block of length 2.
* **3 digits remaining:** A single block of length 3.
* **4 digits remaining:** Two blocks of length 2 each.

The blocks are then separated by dashes. For example, if the remaining digits are `"1234"`, it becomes `"12-34"`.

The overall runtime complexity must be strictly bounded by:

```text
O(n)
```

*(where n is the length of the string number)*

---

## Examples

### Example 1

**Input**

```java
number = "1-234-567"
```

**Output**

```java
"123-45-67"
```

**Explanation**

1. Remove spaces and dashes: `"1234567"`
2. Group into blocks of 3: `"123"`, remaining `"4567"`
3. 4 digits remain: Split into two blocks of length 2: `"45"`, `"67"`
Combined with dashes: `"123-45-67"`.

---

### Example 2

**Input**

```java
number = "123 4-5678"
```

**Output**

```java
"123-456-78"
```

**Explanation**

1. Remove spaces and dashes: `"12345678"`
2. Group into blocks of 3: `"123"`, `"456"`, remaining `"78"`
3. 2 digits remain: A single block of length 2: `"78"`
Combined with dashes: `"123-456-78"`.

---

### Example 3

**Input**

```java
number = "12"
```

**Output**

```java
"12"
```

---

## Brute Force Approach

Clean the string of all spaces and dashes, convert it into an array of characters, and then run multiple tracking condition passes to cut slices from left to right.

### Steps

1. Replace all spaces and dashes globally using regular expressions: `number.replaceAll("[ -]", "")`.
2. Iterate from left to right chunking elements.
3. If the remaining length is greater than 4, take 3 characters, append them along with a dash, and advance.
4. Use conditional checks to slice up the tail segment once length matches 2, 3, or 4.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(n) // Allocates multiple intermediate string buffers via replaceAll
```

While linear asymptotically, calling high-level regex libraries like `.replaceAll()` forces the runtime engine to scan the string fully and allocate intermediate string components onto the heap. We can optimize this process down to a clean, **single-pass extraction** with a zero-allocation pipeline.

---

# Optimal Approach: Two-Pointer Digit-Filtering Buffer

## Key Idea

Instead of performing separate cleaning and chunking passes, we can process the string dynamically from left to right while filling a mutable `StringBuilder` with raw digits only.

Once we have a clean sequence of absolute digits, we can build our final response using index strides:
1. **Clean Digit Extraction:** Iterate through `number` from index `0` up to `n - 1`. If `Character.isDigit(c)` is true, append it to a temporary `StringBuilder`.
2. **Chunk Assembly Loop:** Maintain an index pointer `i = 0` running through the clean digits buffer.
3. While the remaining unparsed digits count `(digits.length() - i) > 4`:
   - Append a 3-character slice followed by a dash delimiter `'-'`.
   - Advance the pointer: `i += 3`.
4. **Tail Segment Resolution:** Once the remaining count drops to 4 or fewer elements, the dynamic loop breaks, and we resolve the tail segment according to the remaining size layout constraints:
   - **If exactly 4 digits remain:** Append 2 characters, a dash `'-'`, and the final 2 characters.
   - **Otherwise (2 or 3 digits remain):** Append all remaining digits together as a single block.

---

## Visual Understanding

Suppose `number = "123 4-5678"` with length 10:

- **Step 1 (Clean Pass):** Extract digits into a flat continuous block \(\implies\) `"12345678"`. Total length = 8.
- **Step 2 (Chunk Loops):** `i = 0`.
  - Remaining length: `8 - 0 = 8 > 4`. Take 3 characters (`"123"`). Append a dash. `sb = "123-"`. Move pointer: `i = 3`.
  - Remaining length: `8 - 3 = 5 > 4`. Take 3 characters (`"456"`). Append a dash. `sb = "123-456-"`. Move pointer: `i = 6`.
  - Remaining length: `8 - 6 = 2`. The condition `2 > 4` is false. Loop breaks.
- **Step 3 (Tail Resolution):** Since exactly 2 digits remain, append them together in a single block without internal dash padding \(\implies\) `sb = "123-456-78"`.

The string is formatted optimally with minimal heap churning.

---

## Partition Variables

Let:

```java
StringBuilder digits = new StringBuilder();
StringBuilder result = new StringBuilder();
int i = 0;
```

---

### Border Elements

The remainder logic evaluates the exact leftover tail footprint, shielding the execution from trailing out-of-bounds index errors:

```java
if (digits.length() - i == 4) {
    result.append(digits.substring(i, i + 2)).append("-").append(digits.substring(i + 2));
} else {
    result.append(digits.substring(i));
}
```

---

## Correct Partition Condition

The block compilation stride loops cleanly as long as the remaining string slice size strictly exceeds 4 elements:

```java
while (digits.length() - i > 4) {
    result.append(digits.substring(i, i + 3)).append("-");
    i += 3;
}
```

---

## Java Solution

```java
class Solution {

    public String reformatNumber(String number) {
        if (number == null || number.length() == 0) {
            return "";
        }

        StringBuilder digits = new StringBuilder();

        // Step 1: In-place filter to collect raw digits only, ignoring spaces and dashes
        for (int idx = 0; idx < number.length(); idx++) {
            char c = number.charAt(idx);
            if (Character.isDigit(c)) {
                digits.append(c);
            }
        }

        StringBuilder result = new StringBuilder();
        int i = 0;
        int len = digits.length();

        // Step 2: Loop to build blocks of size 3 as long as more than 4 digits remain
        while (len - i > 4) {
            result.append(digits.substring(i, i + 3)).append("-");
            i += 3;
        }

        // Step 3: Handle the remaining tail segment of 4 or fewer digits
        if (len - i == 4) {
            // Split 4 remaining digits into two blocks of length 2 each
            result.append(digits.substring(i, i + 2)).append("-").append(digits.substring(i + 2));
        } else {
            // Append the remaining 2 or 3 digits directly as a single block
            result.append(digits.substring(i));
        }

        return result.toString();
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Digit-Filtering Scan** | **O(n)** | **O(n)** | Traverses the raw string input exactly once. The string builders allocate memory slots proportional to the cleaned character length of the phone digits. |

---

## Key Insight

Processing character filtration internally and managing block sizes via incremental step indices removes the need for expensive structural regex updates, reformating values inside an optimal time envelope.

```text
Time  : O(n) single pass linear execution
Space : O(n) dynamic string output builder allocations
```

---

## Similar Problems

1. License Key Formatting (482)
2. String Compression (443)
3. Reverse Words in a String (151)
4. Length of Last Word (58)
5. Rearrange Spaces Between Words (1592)
