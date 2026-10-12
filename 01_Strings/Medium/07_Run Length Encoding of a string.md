# Run-Length Encoding (String Compression)

## Problem Statement

Implement **Run-Length Encoding (RLE)**, a basic form of data compression where consecutive identical characters are replaced by the character followed by its frequency count. 

Given a string `s`, return its compressed run-length encoded string format. For instance, the string `"aaaaaa"` compresses down cleanly to `"a6"`.

If the compressed string does not become shorter than the original input string, some problem variations specify returning the original string itself. However, the standard baseline engine should output the absolute encoded representation.

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
s = "aabbcc"
```

**Output**

```java
"a2b2c2"
```

---

### Example 2

**Input**

```java
s = "aaabcccd"
```

**Output**

```java
"a3b1c3d1"
```

---

### Example 3

**Input**

```java
s = "fffff"
```

**Output**

```java
"f5"
```

---

## Brute Force Approach

Generate individual sliced copies of unique substring components, run a secondary lookahead search inner loop to verify how far the duplicate character chain stretches, and append segments piece by piece.

### Steps

1. Run a loop tracking pointer `i` from `0` to `n - 1`.
2. Extract `s.charAt(i)` and spin up a secondary counter loop starting from `j = i + 1`.
3. Increment the counter as long as `s.charAt(i) == s.charAt(j)`.
4. Append the character followed by the computed count string to a result variable.
5. Fast-forward pointer `i` over to index `j - 1` and repeat.

### Complexity

```text
Time Complexity: O(n) // amortized linear pass
Space Complexity: O(n) due to heavy intermediate string object instantiations
```

While linear asymptotically, repeatedly instantiating fresh sliced intermediate strings or handling manual double-index loops results in bloated code layouts. We can clean this process up significantly into a single unified pass using a single tracker index and an optimized `StringBuilder` buffer.

---

# Optimal Approach: Single-Pass Linear Anchor Tracking

## Key Idea

We can compute the complete compressed sequence in **O(n) time and O(n) space** by tracking the boundaries where character transitions occur.

Instead of writing complex nested loops, we scan the string from left to right using a single loop, maintaining a running `count` register initialized to `1`:
1. Initialize a mutable `StringBuilder`.
2. Run a loop with index pointer `i` from `0` up to `n - 1`.
3. **The Transition Check:** Compare the character at the current index `s.charAt(i)` with the character immediately following it `s.charAt(i + 1)`.
   - **If they match:** Increment our running `count` register by 1 and move to the next index.
   - **If they mismatch OR we reach the final trailing character (`i == n - 1`):** A contiguous character segment has ended. Append `s.charAt(i)` followed by the current accumulated value of `count` straight into our `StringBuilder`. Then, reset the `count` register back to `1` to prepare for the next character run.

---

## Visual Understanding

Suppose `s = "aaabcccd"` with length 8:

- **Initialization:** `count = 1`, `StringBuilder sb = ""`.
- **i = 0 ('a'):** `s.charAt(0) == s.charAt(1)` ('a' == 'a') \(\implies\) `count = 2`.
- **i = 1 ('a'):** `s.charAt(1) == s.charAt(2)` ('a' == 'a') \(\implies\) `count = 3`.
- **i = 2 ('a'):** `s.charAt(2) != s.charAt(3)` ('a' != 'b') \(\implies\) **Transition!** Append `'a'` and `3`. `sb = "a3"`. Reset `count = 1`.
- **i = 3 ('b'):** `s.charAt(3) != s.charAt(4)` ('b' != 'c') \(\implies\) **Transition!** Append `'b'` and `1`. `sb = "a3b1"`. Reset `count = 1`.
- **i = 4 ('c'):** `s.charAt(4) == s.charAt(5)` ('c' == 'c') \(\implies\) `count = 2`.
- **i = 5 ('c'):** `s.charAt(5) == s.charAt(6)` ('c' == 'c') \(\implies\) `count = 3`.
- **i = 6 ('c'):** `s.charAt(6) != s.charAt(7)` ('c' != 'd') \(\implies\) **Transition!** Append `'c'` and `3`. `sb = "a3b1c3"`. Reset `count = 1`.
- **i = 7 ('d'):** Boundary edge `i == n - 1` hit! **Transition!** Append `'d'` and `1`. `sb = "a3b1c3d1"`.

The compressed string is assembled cleanly in a single pass.

---

## Partition Variables

Let:

```java
StringBuilder sb = new StringBuilder();
int count = 1;
int n = s.length();
```

---

### Border Elements

The boundary transition verification condition checks whether the next slot overflows or catches a character mismatch in a single compound line:

```java
if (i + 1 == n || s.charAt(i) != s.charAt(i + 1)) {
    sb.append(s.charAt(i)).append(count);
    count = 1; // Reset counter register
}
```

---

## Correct Partition Condition

The linear accumulation pass processes all array characters from left to right within a single unified loop:

```java
for (int i = 0; i < n; i++) {
    if (i + 1 == n || s.charAt(i) != s.charAt(i + 1)) {
        sb.append(s.charAt(i)).append(count);
        count = 1;
    } else {
        count++;
    }
}
return sb.toString();
```

---

## Java Solution

```java
class Solution {

    public String encode(String s) {
        if (s == null || s.length() == 0) {
            return "";
        }

        StringBuilder sb = new StringBuilder();
        int count = 1;
        int n = s.length();

        // Step 1: Scan the string linearly from left to right
        for (int i = 0; i < n; i++) {
            
            // Step 2: Trigger transition if next char mismatches OR boundary edge is reached
            if (i + 1 == n || s.charAt(i) != s.charAt(i + 1)) {
                sb.append(s.charAt(i));
                sb.append(count);
                
                // Reset counter for the next incoming block
                count = 1;
            } 
            // Step 3: Accumulate duplicate characters inside the current run
            else {
                count++;
            }
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
| **Linear Anchor Scan** | **O(n)** | **O(n)** | Traverses the string exactly once. The `StringBuilder` allocates memory slots proportional to the size of the generated output compressed text. |

---

## Key Insight

Evaluating lookahead transitions (`s.charAt(i) != s.charAt(i + 1)`) removes the need for nested index rewinds or complex sub-pointer logic, encoding character runs in a flat linear pass.

```text
Time  : O(n) single pass forward execution
Space : O(n) dynamic string output builder allocation
```

---

## Similar Problems

1. String Compression / LeetCode 443
2. Decompress Run-Length Encoded List (1313)
3. Count and Say (38)
4. Length of Last Word (58)
5. Number of Segments in a String (434)
