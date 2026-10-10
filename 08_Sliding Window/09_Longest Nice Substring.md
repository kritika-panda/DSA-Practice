# Longest Nice Substring

## Problem Statement

A string `s` is defined as **nice** if, for every letter of the alphabet that `s` contains, it appears in both its uppercase and lowercase forms. 

Given a string `s`, return the **longest substring** of `s` that is nice. If there are multiple nice substrings of the maximum length, return the substring that occurs **earliest**. If no nice substrings exist, return an empty string `""`.

---

## Examples

### Example 1

```text
Input: s = "YazaAay"
Output: "aAa"
```

### Explanation

```text
"aAa" is a nice string because the letter 'A' appears as both 'A' and 'a'. 
It is the longest nice substring found in the input.
```

---

### Example 2

```text
Input: s = "Bb"
Output: "Bb"
```

### Explanation

```text
"Bb" is a nice string because both 'B' and 'b' appear. The entire string is valid.
```

---

### Example 3

```text
Input: s = "c"
Output: ""
```

### Explanation

```text
There are no nice substrings because 'c' lacks its uppercase counterpart 'C'.
```

---

# Key Property: Divide and Conquer / Substring Splitting

```text
[ Left Substring ] <--- [ Violating Character (i) ] ---> [ Right Substring ]
```

If a character in the string lacks its case pair (either uppercase or lowercase) within the current segment, that specific character **can never be part of any nice substring**. 

Consequently, the target nice substring must exist entirely either to the left or to the right of this violating character. This layout naturally lends itself to a **Divide and Conquer** recursive strategy.

---

# Intuition

We examine the string character by character. 

### Case 1: All characters have a matching pair
If we inspect the entire string segment and find that every character has both its lowercase and uppercase forms present within the segment, the entire string itself is valid. We return `s`.

---

### Case 2: A character lacks its matching pair
The first character at index `i` that does not have its matching case counterpart acts as a hard boundary split. We break the problem down recursively:
1. Find the longest nice substring in the **left partition**: `s.substring(0, i)`
2. Find the longest nice substring in the **right partition**: `s.substring(i + 1)`

Finally, we compare the lengths of the results from both partitions. To satisfy the requirement of returning the *earliest occurrence* in case of a length tie, we greedily prefer the left partition if its length is greater than or equal to the right partition.

---

# Visualization

Evaluating `s = "YazaAay"`:

```text
1. Initial Check: 
   - Search for pairs of each character inside "YazaAay".
   - Character 'Y' has 'y' at the end. Valid.
   - Character 'a' has 'A' at index 4. Valid.
   - Character 'z' has NO uppercase 'Z' anywhere in the string! 
   
2. Split at index 2 ('z'):
   - Left side:  "Ya"
   - Right side: "aAay"

3. Process Left Side "Ya":
   - 'Y' has no 'y' in "Ya". Split at 'Y'. 
   - Left = "", Right = "a" -> Both return "" because length < 2.

4. Process Right Side "aAay":
   - 'y' has no 'Y' inside "aAay". Split at 'y' (index 3 of this segment).
   - Left = "aAa", Right = ""
   
5. Process "aAa":
   - All characters ('a' and 'A') have their pairs inside "aAa".
   - Returns "aAa" (Length 3).

6. Final Comparison:
   - Left partition ("Ya") gave ""
   - Right partition ("aAay") gave "aAa"
   
Answer: "aAa"
```

---

# Java Implementation

```java
class Solution {
    public String longestNiceSubstring(String s) {
        // Base case: a nice string requires at least one lowercase and one uppercase character
        if (s.length() < 2) {
            return "";
        }
        
        // Scan the string to find any character that violates the nice property
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            
            // If the counterpart of the character is missing, split the string
            if (s.indexOf(Character.toLowerCase(c)) == -1 || 
                s.indexOf(Character.toUpperCase(c)) == -1) {
                
                // Recursively find the longest nice substring on both sides of the violation
                String left = longestNiceSubstring(s.substring(0, i));
                String right = longestNiceSubstring(s.substring(i + 1));
                
                // Return left if it's longer or equal (maintains earliest occurrence rule)
                return left.length() >= right.length() ? left : right;
            }
        }
        
        // If the loop completes without returning, the entire string is nice
        return s; 
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N²) where N is the length of the string `s`. In the worst-case scenario (e.g., repeating configurations that trigger cascading splits), the function will make recursive calls on substrings, performing O(N) string index operations at each tree depth level.
* **Space Complexity:** O(N) auxiliary space. The system allocates memory frames matching the maximum recursion depth, which scales proportionally with the length of the string in the worst-case unbalanced execution tree.
