# Aggressive Cows (Maximize Minimum Distance)

The **Aggressive Cows** problem is a classic Data Structures and Algorithms (DSA) problem that requires finding an optimal allocation strategy using **Binary Search on Answer Space** combined with a **Greedy Evaluation** check function.

---

## 📝 Problem Statement

Farmer John has built a new long barn with N stalls located along a straight line at distinct integer positions (x_1, x_2, dots, x_N). 

He has K aggressive cows that do not like this layout and will attack each other if placed too close together. To prevent them from hurting one another, he wants to assign the cows to the stalls such that the **minimum distance between any two of them is as large as possible**. 

Your task is to find the **largest possible minimum distance**.

### Input Format
* The first line contains two integers: N (the number of stalls) and K (the number of aggressive cows).
* The second line contains N space-separated integers representing the coordinates of each stall.

### Output Format
* Print a single integer representing the maximum possible minimum distance.

---

## 💡 Example

### Sample Input
```text
stalls = [1, 2, 8, 4, 9]
k = 3
```

### Sample Output
```text
3
```

### Explanation
1. Sort the stall positions: `[1, 2, 4, 8, 9]`.
2. If we place the 3 cows at positions `1`, `4`, and `8`:
   * Distance between Cow 1 and Cow 2: |4 - 1| = 3
   * Distance between Cow 2 and Cow 3: |8 - 4| = 4
   * The minimum distance here is (min(3, 4) = 3).
3. Any attempt to place them with a minimum gap of `4` or more will fail to accommodate all 3 cows. Thus, `3` is the largest configuration.

---

## 🛠️ Algorithmic Approach

The problem exhibits a **monotonic property**, making it perfect for **Binary Search**:
* If it is possible to arrange the cows with a minimum distance D, then any distance smaller than D is also guaranteed to be possible.
* If it is impossible to arrange them with a minimum distance D, then any distance greater than D will definitely fail.

### Step-by-Step Logic
1. **Sort:** Sort the array of stall positions in ascending order. This allows a single left-to-right pass during the validation check.
2. **Define Search Space:** 
   * `low = 1` (The smallest logical gap between distinct integers).
   * `high = stalls[N-1] - stalls[0]` (The maximum possible gap spanning the entire array).
3. **Binary Search:** 
   * Compute `mid = low + (high - low) / 2`.
   * Use a greedy validation helper function `canPlaceCows(mid)` to check if K cows can be accommodated with at least `mid` distance between them.
   * If **True**, update your tracked answer to `mid` and step right (`low = mid + 1`) to find if an even larger minimum distance is possible.
   * If **False**, step left (`high = mid - 1`) to test a smaller, less restrictive distance.

---

## 💻 Code Implementations

### 1. Java Solution
```java
import java.util.Arrays;

public class AggressiveCows {

    // Greedy validation helper function
    public static boolean canPlaceCows(int[] stalls, int k, int minDist) {
        int cowsPlaced = 1;
        int lastPosition = stalls[0]; // Greedily place the first cow in the first stall

        for (int i = 1; i < stalls.length; i++) {
            if (stalls[i] - lastPosition >= minDist) {
                cowsPlaced++;
                lastPosition = stalls[i]; // Update the position of the last placed cow
                
                if (cowsPlaced >= k) {
                    return true;
                }
            }
        }
        return false;
    }

    public static int getMaximumMinimumDistance(int[] stalls, int k) {
        // Step 1: Sort the stall positions
        Arrays.sort(stalls);

        // Step 2: Initialize the search space bounds
        int low = 1;
        int high = stalls[stalls.length - 1] - stalls[0];
        int result = 0;

        // Step 3: Execute Binary Search on Answer Space
        while (low <= high) {
            int mid = low + (high - low) / 2;

            if (canPlaceCows(stalls, k, mid)) {
                result = mid;       // Greedily save the working configuration
                low = mid + 1;      // Try to find a larger minimum distance
            } else {
                high = mid - 1;     // Restrict to a smaller distance limit
            }
        }
        return result;
    }

    // Driver Code
    public static void main(String[] args) {
        int[] stalls = {1, 2, 4, 8, 9};
        int k = 3;

        System.out.println("Maximum possible minimum distance: " + getMaximumMinimumDistance(stalls, k));
    }
}
```

---

## 📊 Complexity Analysis

* **Time Complexity:** O(N log N + N log D)
  * **Sorting:** Takes O(N log N) time.
  * **Binary Search Space:** Iterates log D times, where D = (stalls[N-1] - stalls[0]).
  * **Feasibility Check:** For every binary step, we do a linear scan through the array which takes O(N) time.
* **Auxiliary Space Complexity:** O(1) or O(log N) depending strictly on the space used by the underlying sorting function framework.
