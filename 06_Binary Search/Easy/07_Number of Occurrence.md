# Count Occurrences in a Sorted Array

## Problem Statement

Given a sorted array of integers `arr` and a `target` value, find the total number of times the `target` appears in the array.

If the `target` is not present in the array, return `0`.

---

## Examples

### Example 1

```text
Input: arr =, target = 2
Output: 4
```

### Explanation

```text
The target 2 occurs 4 times in the given array at indices 2, 3, 4, and 5.
```

---

### Example 2

```text
Input: arr =, target = 4
Output: 0
```

### Explanation

```text
The target 4 does not exist in the array, so it occurs 0 times.
```

---

# Key Property: Index Interval Arithmetic

```text
Total Frequency = Last_Occurrence_Index - First_Occurrence_Index + 1
```

Because the array is sorted, all duplicate occurrences of the `target` are guaranteed to sit adjacent to each other in a contiguous block. 

Instead of searching for a match and linearly scanning left and right to count duplicates—which would degenerate to \(O(N)\) time complexity in the worst case—we can run two separate modified binary searches to lock down the **exact start and end indices** of the target block in \(O(\log N)\) time.

---

# Intuition

We divide the process into three core phases:

### Phase 1: Locate the First Occurrence
We run a modified binary search `findFirst`. If `arr[mid] == target`, we save `ans = mid`. Instead of stopping, we contract the upper search bound (`high = mid - 1`) to explore the left half, looking for any earlier duplicate entries.

### Phase 2: Locate the Last Occurrence
We run a second modified binary search `findLast`. If `arr[mid] == target`, we save `ans = mid`. We then expand the lower search bound (`low = mid + 1`) to check the right half for any later duplicate entries.

### Phase 3: Compute the Range Difference
If `findFirst` returns `-1`, the target is missing entirely, and we return `0`. Otherwise, the size of the target block is simply the difference between the boundaries: `last - first + 1`.

---

# Java Implementation

```java
class Solution {
    int countFreq(int[] arr, int target) {
        // Step 1: Find the first occurrence index of the target
        int first = findFirst(arr, target);
        
        // If the target doesn't exist, its frequency is 0
        if (first == -1) return 0; 
        
        // Step 2: Find the last occurrence index of the target
        int last = findLast(arr, target);
        
        // Step 3: Compute total frequency via boundary subtraction
        return last - first + 1;
    }

    // Binary search routine to locate the leftmost target index
    private int findFirst(int[] arr, int target) {
        int low = 0, high = arr.length - 1, ans = -1;
        while (low <= high) {
            int mid = (low + high) / 2;
            if (arr[mid] == target) {
                ans = mid;
                high = mid - 1; // Keep searching left for earlier duplicates
            } else if (arr[mid] > target) {
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }

    // Binary search routine to locate the rightmost target index
    private int findLast(int[] arr, int target) {
        int low = 0, high = arr.length - 1, ans = -1;
        while (low <= high) {
            int mid = (low + high) / 2;
            if (arr[mid] == target) {
                ans = mid;
                low = mid + 1; // Keep searching right for later duplicates
            } else if (arr[mid] > target) {
                high = mid - 1; 
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
}
```

### Complexity Analysis

* **Time Complexity:** \(O(\log N)\) where \(N\) is the total number of elements in `arr`. The algorithm executes two consecutive binary searches. Each search divides the active search window strictly in half at every step, maintaining a highly optimal logarithmic runtime constraint.
* **Space Complexity:** \(O(1)\) auxiliary space. The calculation runs entirely in-place, tracking ranges using only a few local primitive integer variables.
