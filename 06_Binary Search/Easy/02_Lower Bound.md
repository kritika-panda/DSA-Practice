# Implement Lower Bound

## Problem Statement

Given a sorted array of integers `arr` in ascending order and a `target` value, find the index of the **lower bound** of the target.

### Definition

The **lower bound** of a target is defined as the index of the **first element** in the sorted array that is **greater than or equal to** the `target`. If all elements in the array are strictly smaller than the target, return the length of the array (`arr.length`).

---

## Examples

### Example 1

```text
Input: arr =, target = 5
Output: 2
```

### Explanation

```text
The elements greater than or equal to 5 are 8, 10, 11, 12, 19.
The first element among these is 8, which is located at index 2.
```

---

### Example 2

```text
Input: arr =, target = 0
Output: 0
```

### Explanation

```text
The first element greater than or equal to 0 is 1, located at index 0.
```

---

### Example 3

```text
Input: arr =, target = 25
Output: 7
```

### Explanation

```text
All elements in the array are strictly smaller than 25. 
Therefore, the function returns the size of the array, which is 7.
```

---

# Key Property: Binary Search Condition

```text
[ Low Index ] ---------> [ Mid Index (arr[mid] >= target) ] <--------- [ High Index ]
                                   |
                         Potential Lower Bound Candidate
                         (Save index, check left half)
```

Because the array is sorted, we can adapt the standard Binary Search strategy. Instead of terminating immediately when `target == arr[mid]`, we treat elements that satisfy `arr[mid] >= target` as **potential lower bound candidates**. We record this index and continue narrowing our search space toward the left to verify if an even earlier matching index exists.

---

# Intuition

We maintain two pointers, `low` and `high`, to anchor our active search window, along with an answer tracking variable `val` initialized to `arr.length`.

At each step of our binary search loop:
1. Find the middle element index: `mid = (low + high) / 2`.
2. **Case 1:** `target <= arr[mid]`. The middle element is a valid lower bound candidate because its value is greater than or equal to the target. 
   * We record this index: `val = mid`.
   * We shrink our search window by moving the upper boundary to look for an earlier matching index in the left half: `high = mid - 1`.
3. **Case 2:** `target > arr[mid]`. The middle element is too small to be a lower bound. The valid candidate must lie strictly to the right. We shift our lower boundary: `low = mid + 1`.

Once `low` crosses `high`, the search loop terminates, and `val` holds the exact index of the leftmost lower bound element.

---

# Java Implementation

```java
class Solution {
    int lowerBound(int[] arr, int target) {
        int low = 0;
        int high = arr.length - 1;
        
        // Default value if all elements are smaller than the target
        int val = arr.length;  
        
        while (low <= high) {
            int mid = (low + high) / 2;
            
            // If current element is a valid lower bound candidate
            if (target <= arr[mid]) {
                val = mid;       // Record the candidate index
                high = mid - 1;  // Move left to search for an earlier occurrence
            } 
            // If current element is smaller than target, move right
            else {
                low = mid + 1;
            }
        }
        
        return val;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(log N) where N is the total length of the `arr` array. The remaining search window is cut exactly in half at each iteration step, ensuring a clean logarithmic runtime profile.
* **Space Complexity:** O(1) auxiliary space. The computation executes completely in-place, maintaining only a few local primitive integer variables (`low`, `high`, `mid`, and `val`).
