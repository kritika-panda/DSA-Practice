# Reverse Vowels of a String

## Problem Statement

Given a string `s`, reverse only all the vowels in the string and return it.

The vowels are `'a'`, `'e'`, `'i'`, `'o'`, and `'u'`, and they can appear in both lower and upper cases, more than once.

The overall runtime complexity must be:

```text
O(n)
```

*(where n is the length of the string s)*

---

## Examples

### Example 1

**Input**

```java
s = "icecreAm"
```

**Output**

```java
"Acecreim"
```

**Explanation**

The vowels in `s` are `['i', 'e', 'e', 'A']`. Reversing these vowels transforms the string into `"Acecreim"`.

---

### Example 2

**Input**

```java
s = "leetcode"
```

**Output**

```java
"leotcede"
```

**Explanation**

The vowels in `s` are `['e', 'e', 'o', 'e']`. Reversing them yields `"leotcede"`.

---

### Example 3

**Input**

```java
s = "XYZ"
```

**Output**

```java
"XYZ"
```

**Explanation**

There are no vowels present in the string, so the output remains completely unchanged.

---

## Brute Force Approach

Extract all vowels into a separate list or stack in a first pass, then traverse the string a second time, replacing vowels sequentially by reading them backward from the list.

### Steps

1. Traverse the string from left to right and collect every character that matches a vowel into an auxiliary list.
2. Initialize a pointer at the end of the collected vowels list.
3. Convert the original string to a mutable character array.
4. Iterate through the array. Whenever a vowel is encountered, replace it with the vowel from the collected list (moving your list pointer backward) and advance.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(n) to store the extracted vowels and character arrays
```

While this linear pass works, it forces double-traversal overhead and duplicates data segments unnecessarily. We can achieve a cleaner, **single-pass solution** by scanning from both ends simultaneously using two pointers.

---

# Optimal Approach: Two-Pointer Symmetrical Collision

## Key Idea

We can isolate and reverse the vowels in a single parallel sweep using a **Two-Pointer technique**, mirroring the classic string inversion strategy but skipping non-vowel components.

1. Convert the string into a mutable primitive `char[] arr` array to perform modifications in-place.
2. Place a `left` pointer at index `0` and a `right` pointer at index `arr.length - 1`.
3. Run a loop that continues as long as `left < right`:
   - **Step A:** Move the `left` pointer forward if `arr[left]` is not a vowel.
   - **Step B:** Move the `right` pointer backward if `arr[right]` is not a vowel.
   - **Step C:** If both pointers are locked onto valid vowels, swap `arr[left]` and `arr[right]`. Then, step inward by incrementing `left` and decrementing `right`.

To make vowel checks extremely fast and constant-time, we can query a pre-populated lookup string or boolean array.

---

## Visual Understanding

Suppose `s = "leetcode"`:

- **Initial Array State:** `['l', 'e', 'e', 't', 'c', 'o', 'd', 'e']`, `left = 0`, `right = 7`.
- **Iteration 1:**
  - `left = 0 ('l')` is not a vowel \(\implies\) `left++` to `1`.
  - `right = 7 ('e')` is a vowel \(\implies\) pointer holds.
- **Iteration 2 (`left = 1 ('e'), right = 7 ('e')`):**
  - Both are vowels. Swap `arr[1]` and `arr[7]` (both are 'e', no visible change here).
  - Advance: `left = 2`, `right = 6`.
- **Iteration 3:**
  - `left = 2 ('e')` is a vowel \(\implies\) pointer holds.
  - `right = 6 ('d')` is not a vowel \(\implies\) `right--` to `5`.
- **Iteration 4 (`left = 2 ('e'), right = 5 ('o')`):**
  - Both are vowels. Swap `arr[2]` ('e') and `arr[5]` ('o').
  - Array becomes: `['l', 'e', 'o', 't', 'c', 'e', 'd', 'e']`.
  - Advance: `left = 3`, `right = 4`.
- **Iteration 5:** Pointers advance past non-vowels and cross. Loop exits.

The vowels are rearranged successfully in a single parallel scan.

---

## Partition Variables

Let:

```java
char[] arr = s.toCharArray();
int left = 0;
int right = arr.length - 1;
```

---

### Border Elements

The internal pointer loops must continually re-verify the global boundary condition `left < right` to prevent indices from crossing or drifting out-of-bounds while skipping non-vowel sequences:

```java
while (left < right && !isVowel(arr[left])) {
    left++;
}
```

---

## Correct Partition Condition

The helper validation checks for character matches across both lower and upper case spectrum rules efficiently:

```java
private boolean isVowel(char c) {
    return "aeiouAEIOU".indexOf(c) != -1;
}
```

---

## Java Solution

```java
class Solution {

    public String reverseVowels(String s) {
        if (s == null || s.length() <= 1) {
            return s;
        }

        // Step 1: Convert string to a mutable character array
        char[] arr = s.toCharArray();
        int left = 0;
        int right = arr.length - 1;

        // Step 2: Symmetrically scan inward from both ends
        while (left < right) {
            
            // Advance left pointer until it hits a vowel
            while (left < right && !isVowel(arr[left])) {
                left++;
            }
            
            // Regress right pointer until it hits a vowel
            while (left < right && !isVowel(arr[right])) {
                right--;
            }

            // If pointers haven't crossed, swap the isolated vowels
            if (left < right) {
                char temp = arr[left];
                arr[left] = arr[right];
                arr[right] = temp;
                
                // Step inward to continue processing
                left++;
                right--;
            }
        }

        // Step 3: Convert the modified character array back to a String
        return new String(arr);
    }

    private boolean isVowel(char c) {
        // Fast constant-time lookup for upper and lower case vowels
        return "aeiouAEIOU".indexOf(c) != -1;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

Where `n` is the total number of characters in the string `s`. The `left` and `right` pointers move inward continuously and look at each index character at most once during the entire execution.

---

### Space Complexity

```text
O(n)
```

The algorithm allocates a primitive character array of length `n` to modify character codes in-place before reconstructing the output string. No dynamic data graphs or recursive tracking memory frames are populated.

---

## Key Insight

Using nested boundary-guarded loops inside a two-pointer structural frame skips unneeded character sets instantly, matching and swapping isolated data subsets in a single parallel sweep.

```text
Time  : O(n) single pass execution profile
Space : O(n) primitive character array allocation
```

---

## Similar Problems

1. Reverse String (344)
2. Valid Palindrome (125)
3. Reverse Words in a String (151)
4. Remove Vowels from a String (1119)
5. Sort Characters By Frequency (451)
