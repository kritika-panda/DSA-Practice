# Search in Rotated Sorted Array II

## Problem Statement

There is an integer array `nums` sorted in ascending order (which **may contain duplicates**).

Prior to being passed to your function, `nums` is possibly rotated at an unknown pivot index `k` (`1 <= k < nums.length`) such that the resulting array becomes `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]` (**0-indexed**). For example, `[0,1,2,4,4,4,5,6,7]` might be rotated at pivot index `4` and become `[4,4,5,6,7,0,1,2,4]`.

Given the array `nums` **after** the rotation and an integer `target`, return `true` if `target` is in `nums`, or `false` if it is not in `nums`.

You must reduce the overall execution steps as much as possible compared to a linear search.

---

## Examples

### Example 1

```text
Input: nums =, target = 0
Output: true
```

---

### Example 2

```text
Input: nums =, target = 3
Output: false
```

---

# Key Property: Duplicate-Driven Ambiguity

```text
When nums[low] == nums[mid] == nums[high], global and local sortedness cannot be determined.
```

In the standard version of this problem (with distinct values), the comparison `nums[low] <= nums[mid]` instantly tells us if the left half is sorted. However, when **duplicates** are introduced, we can encounter a scenario where the elements at `low`, `mid`, and `high` are completely identical. 

```text
Array: [1, 0, 1, 1, 1, 1, 1] ---> low = 0, mid = 3, high = 6
Values: nums[0] == 1, nums[3] == 1, nums[6] == 1
```

In this state, it is mathematically impossible to know whether the left half or the right half contains the rotation pivot drop. To resolve this ambiguity, we must perform a **linear trim** by shifting both boundary pointers inward (`low++` and `high--`) until the edge values change.

---

# Intuition

We maintain two pointers, `low` and `high`, to track our active search window.

At each step inside the binary search loop:
1. Find the midpoint: `mid = (low + high) / 2`.
2. **Target Found:** If `arr[mid] == k`, return `true` immediately.
3. **Handle Duplicates:** If `arr[low] == arr[mid] && arr[mid] == arr[high]`, we cannot determine which side is sorted. We shrink the search space from both ends (`low++`, `high--`) and jump to the next loop iteration using `continue`.
4. **Determine Sorted Half:** Once the edge elements are distinct, we resume our standard rotated binary search pattern:
   * **Case A: Left Half is Sorted** (`arr[low] <= arr[mid]`)
     * If the target is bounded inside this left segment (`arr[low] <= k && k <= arr[mid]`), move left: `high = mid - 1`.
     * Otherwise, search the right half: `low = mid + 1`.
   * **Case B: Right Half is Sorted** (`arr[low] > arr[mid]`)
     * If the target is bounded inside this right segment (`arr[mid] <= k && k <= arr[high]`), move right: `low = mid + 1`.
     * Otherwise, search the left half: `high = mid - 1`.

If the pointers cross and the target was never hit, return `false`.

---

# Java Implementation

```java
class Solution {
    public boolean search(int[] arr, int k) {
        int low = 0, high = arr.length - 1;
        
        while (low <= high) {
            int mid = (low + high) / 2;
            
            // If target is found, return true
            if (arr[mid] == k) {
                return true;
            }
            
            // Edge Case: Handle duplicates that create boundary ambiguity
            // If the edges and mid match, we cannot identify the sorted half
            if (arr[low] == arr[mid] && arr[mid] == arr[high]) {
                low++;
                high--;
                continue; // Skip directly to the next midpoint calculation
            }
            
            // Condition 1: Check if the left half is sorted
            if (arr[low] <= arr[mid]) {
                // Check if target lies within the sorted left boundaries
                if (arr[low] <= k && k <= arr[mid]) {
                    high = mid - 1; // Search left
                } else {
                    low = mid + 1;  // Search right
                }
            } 
            // Condition 2: The right half must be sorted instead
            else {
                // Check if target lies within the sorted right boundaries
                if (arr[mid] <= k && k <= arr[high]) {
                    low = mid + 1;  // Search right
                } else {
                    high = mid - 1; // Search left
                }
            }
        }
        return false;
    }
}
```

### Complexity Analysis

* **Time Complexity:** 
  * **Average Case:** \(O(\log N)\). When elements are mostly distinct or duplicates do not align on the search boundaries, the search space halves at every step.
  * **Worst Case:** O(N). If the array contains almost entirely identical elements (e.g., `[1, 1, 1, 1, 0, 1, 1]`), the algorithm is forced to continually execute the duplicate trimming step (`low++`, `high--`), reducing the performance profile to a linear scan.
* **Space Complexity:** O(1) auxiliary space. All operations run strictly in-place using only basic pointer index adjustments.
