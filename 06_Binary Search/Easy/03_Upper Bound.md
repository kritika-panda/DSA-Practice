# Implement Upper Bound

## Problem Statement

Given a sorted array of integers `arr` in ascending order and a `target` value, find the index of the **upper bound** of the target.

### Definition

The **upper bound** of a target is defined as the index of the **first element** in the sorted array that is **strictly greater than** the `target`. If no such element exists (i.e., all elements in the array are less than or equal to the target), return the length of the array (`arr.length`).

---

## Examples

### Example 1

```text
Input: arr =, target = 5
Output: 2
```

### Explanation

```text
The elements strictly greater than 5 are 8, 10, 11, 12, 19.
The first element among these is 8, which is located at index 2.
```

---

### Example 2

```text
Input: arr =, target = 5
Output: 4
```

### Explanation

```text
The elements strictly greater than 5 are 8, 12, 19.
The first element among these is 8, located at index 4.
```

---

### Example 3

```text
Input: arr =, target = 25
Output: 7
```

### Explanation

```text
There are no elements in the array strictly greater than 25. 
Therefore, the function returns the size of the array, which is 7.
```

---

# Key Property: Binary Search Condition

```text
[ Low Index ] ---------> [ Mid Index (arr[mid] > target) ] <--------- [ High Index ]
                                   |
                         Potential Upper Bound Candidate
                         (Save index, check left half)
```

Because the array is sorted, we can use binary search to locate the boundary line. Unlike a standard binary search or lower bound implementation, we are strictly looking for elements where `arr[mid] > target`. When we locate a middle element that satisfies this inequality, we record the index as a **potential upper bound candidate** and then shift our search space towards the left to verify if an even earlier valid element exists.

---

# Intuition

We maintain two pointers, `low` and `high`, to anchor our active search window, along with an answer tracking variable `val` initialized to `arr.length`.

At each step of our binary search loop:
1. Find the middle element index: `mid = (low + high) / 2`.
2. **Case 1:** `arr[mid] > target`. The middle element is strictly greater than our target, making it a valid upper bound candidate. 
   * We record this index: `val = mid`.
   * We contract our search window by moving the upper boundary to check for an earlier valid index in the left half: `high = mid - 1`.
3. **Case 2:** `arr[mid] <= target`. The middle element is less than or equal to the target. This element and everything to its left cannot be the upper bound. The valid candidate must lie strictly to the right. We shift our lower boundary: `low = mid + 1`.

Once `low` crosses `high`, the search loop terminates, and `val` holds the exact index of the leftmost element strictly greater than the target.

---

# Java Implementation

```java
class Solution {
    int upperBound(int[] arr, int target) {
        int low = 0, high = arr.length - 1;
        
        // Default value if no element is strictly greater than the target
        int val = arr.length; 
        
        while (low <= high) {
            int mid = (low + high) / 2;
            
            // If the current element is strictly greater than the target
            if (arr[mid] > target) {
                val = mid;       // Record the candidate index
                high = mid - 1;  // Move left to see if an earlier element also qualifies
            } 
            // If the current element is less than or equal to the target, move right
            else {
                low = mid + 1;   // Search right
            }
        }
        return val;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(log N) where N is the total length of the `arr` array. The remaining search window is cut exactly in half at each iteration step, ensuring a clean logarithmic runtime profile.
* **Space Complexity:** O(1) auxiliary space. The computation executes completely in-place, maintaining only a few local primitive integer variables (`low`, `high`, `mid`, and `val`).
