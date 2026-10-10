# Floor and Ceil in a Sorted Array

## Problem Statement

You are given a sorted array `arr` of `n` integers and an integer `x`. Find the **floor** and **ceiling** of `x` in the array.

### Definitions

* **Floor of x:** The largest element in the array which is smaller than or equal to `x`. If no such element exists, return `-1`.
* **Ceiling of x:** The smallest element in the array which is greater than or equal to `x`. If no such element exists, return `-1`.

---

## Examples

### Example 1

```text
Input: arr =, x = 5
Result: 4 7
```

### Explanation

```text
The largest element in the array smaller than or equal to 5 is 4 (Floor = 4).
The smallest element in the array greater than or equal to 5 is 7 (Ceiling = 7).
```

---

### Example 2

```text
Input: arr =, x = 8
Result: 8 8
```

### Explanation

```text
The element 8 exists in the array, making it both the floor and the ceiling of 8.
```

---

# Key Concept: Binary Search Optimization

```text
Floor Optimization:   arr[mid] <= x  ---> Record value, check right half for larger values
Ceiling Optimization: arr[mid] >= x  ---> Record value, check left half for smaller values
```

Because the input array is already sorted in ascending order, we can solve both operations independently using modified binary searches instead of linear scanning.
* **Ceiling** is identical to the classic **Lower Bound** definition.
* **Floor** reverses the search direction; when we find a value less than or equal to `x`, it becomes a valid floor candidate, and we look to the right to see if a larger valid floor candidate exists.

---

# Intuition

### 1. Finding the Floor
We maintain a candidate tracker variable `ans` initialized to `-1`. While traversing the binary search bounds:
* If `arr[mid] <= x`, then `arr[mid]` is a valid floor candidate. We record it (`ans = arr[mid]`) and eliminate the left half by moving `low = mid + 1` to search for a potentially larger valid candidate.
* If `arr[mid] > x`, the current element is too large to be a floor. We eliminate the right half by moving `high = mid - 1`.

### 2. Finding the Ceiling
We maintain a candidate tracker variable `ans` initialized to `-1`. While traversing the binary search bounds:
* If `arr[mid] >= x`, then `arr[mid]` is a valid ceiling candidate. We record it (`ans = arr[mid]`) and eliminate the right half by moving `high = mid - 1` to search for a potentially smaller valid candidate.
* If `arr[mid] < x`, the current element is too small to be a ceiling. We eliminate the left half by moving `low = mid + 1`.

---

# Java Implementation

```java
import java.util.*;

class FloorCeilFinder {
    // Function to find the floor of x
    public int findFloor(int[] arr, int x) {
        int low = 0, high = arr.length - 1;
        int ans = -1;

        while (low <= high) {
            int mid = (low + high) / 2;
            
            // If current element is less than or equal to x, it's a floor candidate
            if (arr[mid] <= x) {
                ans = arr[mid];     // Record the potential floor value
                low = mid + 1;      // Look right to find a larger valid element
            } else {
                high = mid - 1;     // Look left for smaller values
            }
        }
        return ans;
    }

    // Function to find the ceiling of x
    public int findCeil(int[] arr, int x) {
        int low = 0, high = arr.length - 1;
        int ans = -1;

        while (low <= high) {
            int mid = (low + high) / 2;
            
            // If current element is greater than or equal to x, it's a ceiling candidate
            if (arr[mid] >= x) {
                ans = arr[mid];     // Record the potential ceiling value
                high = mid - 1;     // Look left to find a smaller valid element
            } else {
                low = mid + 1;      // Look right for larger values
            }
        }
        return ans;
    }

    // Function to combine and return floor and ceil as an array
    public int[] getFloorAndCeil(int[] arr, int x) {
        int f = findFloor(arr, x);
        int c = findCeil(arr, x);
        return new int[]{f, c};
    }

    public static void main(String[] args) {
        int[] arr = {3, 4, 4, 7, 8, 10};
        int x = 5;
        FloorCeilFinder finder = new FloorCeilFinder();
        int[] res = finder.getFloorAndCeil(arr, x);
        System.out.println("The floor and ceil are: " + res[0] + " " + res[1]);
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(log N) where N is the total length of the `arr` array. The algorithm runs two independent binary searches. Each binary search cuts the active search window exactly in half at every step, keeping the runtime strictly logarithmic.
* **Space Complexity:** O(1) auxiliary space. The logic performs its evaluations completely in-place using only local primitive integer variables.
