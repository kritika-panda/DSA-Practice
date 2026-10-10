# Longest Repeating Character Replacement

## Problem Statement

Given a string `s` consisting of uppercase English letters and an integer `k`, you can choose any character of the string and change it to any other uppercase English character. You can perform this operation at most `k` times.

After performing these steps, return the length of the **longest substring** containing the same letter.

---

## Examples

### Example 1

```text
Input: s = "BAABAABBBAAA", k = 2  
Output: 6  
```

Because:

```text
We can change the 'B' at index 0 and index 3 to 'A'. 
The modified string becomes "AAAAAABBBAAA". 
The substring "AAAAAA" is the longest substring with identical letters, yielding a length of 6.
```

---

### Example 2

```text
Input: s = "AABABBA", k = 1  
Output: 4  
```

Because:

```text
We can change the 'A' at index 2 to 'B' to get the string "AABBBBA". 
The substring "BBBB" is the longest containing identical characters, yielding a length of 4.
```

---

# Key Concept: Sliding Window

```text
[ Window Start (left) ... Window End (right) ]
```

Instead of trying all possible character substitutions, we maintain a dynamic sliding window. At any point, the number of replacements required to make all characters in the window identical is calculated by:

```text
Replacements Needed = Window_Length - Max_Frequency_Character_Count
```

---

# Intuition

We expand the window using the `right` pointer and record character frequencies inside a fixed-size array. We also track `maxCount`, which represents the maximum frequency of *any single character seen so far* across the execution history.

### Case 1: Window is Valid
If `(Window_Length) - maxCount <= k`, it means we have enough replacements (`k`) available to convert all other characters in the window to match the most frequent character. The window is valid, so we record its size and expand `right`.

---

### Case 2: Window is Invalid
If `(Window_Length) - maxCount > k`, the current window requires more than `k` modifications, making it invalid. We shrink the window from the left by decrementing the frequency of `s.charAt(left)` and advancing the `left` pointer forward until the condition becomes valid again.

---

## Optimization Note: Why We Don't Recalculate `maxCount`

When shrinking the window, we **do not** decrease `maxCount` even if the character that left the window was the dominant one. 

1. **Efficiency:** Recalculating the true maximum frequency during every window contraction would require scanning the frequency array, changing our linear time complexity into an inefficient performance profile.
2. **Correctness:** We are only looking for a window *larger* than the maximum valid window found so far. To expand our global max length, we would need a new character sequence that beats our historic `maxCount` anyway. Keeping `maxCount` artificially high causes the window to contract earlier rather than later, which safely preserves the correctness of the final largest window size.

---

# Java Implementation

```java
class Solution {
    // Function to return the length of the longest substring that can be made of repeating characters
    // by replacing at most k characters
    public int characterReplacement(String s, int k) {
        // Frequency array for A-Z
        int[] freq = new int[26];

        // Left and right pointers of sliding window
        int left = 0, right = 0;

        // Tracks the count of the most frequent character in current window
        int maxCount = 0;

        // Stores the maximum length of valid window
        int maxLength = 0;

        // Iterate through the string with right pointer
        while (right < s.length()) {

            // Increment the frequency of current character
            freq[s.charAt(right) - 'A']++;

            // Update maxCount with the max frequency seen so far
            maxCount = Math.max(maxCount, freq[s.charAt(right) - 'A']);

            // If the current window needs more than k replacements, move left
            while ((right - left + 1) - maxCount > k) {
                freq[s.charAt(left) - 'A']--;
                left++;
            }

            // Update the maximum window length
            maxLength = Math.max(maxLength, right - left + 1);

            // Move right pointer forward
            right++;
        }

        // Return the maximum valid window length
        return maxLength;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the length of the string `s`. Each character is processed at most twice—once by the `right` pointer expanding the window, and at most once by the `left` pointer contracting it. All internal updates inside the loop execute in constant time.
* **Space Complexity:** O(1) auxiliary space. The algorithm uses a fixed-size integer array of length 26 to track English alphabet frequencies regardless of how large the input string grows.
