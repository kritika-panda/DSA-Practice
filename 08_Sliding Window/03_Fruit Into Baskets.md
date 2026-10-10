# Fruit Into Baskets

## Problem Statement

You are visiting a farm that has a single row of fruit trees arranged from left to right. The trees are represented by an integer array `fruits` where `fruits[i]` is the type of fruit the \(i^{th}\) tree produces.

You want to collect as much fruit as possible. However, the owner has some strict rules that you must follow:
1. You only have **two baskets**, and each basket can only hold a **single type of fruit**. There is no limit on the amount of fruit each basket can hold.
2. Starting from any tree of your choice, you must pick **exactly one fruit from every tree** (including the start tree) while moving to the right. The picked fruits must fit in one of your baskets.
3. Once you reach a tree with fruit that cannot fit in your baskets, you must stop.

Given the integer array `fruits`, return the **maximum number of fruits** you can pick.

---

## Examples

### Example 1

```text
Input: fruits = [1,2,1]
Output: 3
```

Because:

```text
We can pick from all 3 trees since there are only two unique types of fruits (1 and 2).
Total fruits collected = 3.
```

---

### Example 2

```text
Input: fruits = [0,1,2,2]
Output: 3
```

Because:

```text
We can pick from trees [1,2,2] starting from the second tree. 
If we had started at the first tree, we would have picked [0,1] and been forced to stop at 2.
Maximum fruits collected = 3.
```

---

# Key Concept: Sliding Window

```text
[ Window Start (l) ... Window End (r) ]
```

This problem can be simplified into a classic substring/subarray problem: **Find the length of the longest contiguous subarray that contains at most two unique integers.** We can solve this efficiently using a two-pointer sliding window technique.

---

# Intuition

We use a right pointer `r` to slide across the array and collect fruits. We track the unique fruit types inside our current window using a **HashMap**, where the key is the fruit type and the value is its frequency count inside the window.

At every step as `r` expands the window:

### Case 1: Map size is at most 2
As long as `HashMap.size() <= 2`, our baskets can hold all the types of fruits currently within the window. The window is valid. We calculate the total fruits picked as `r - l + 1` and update our global maximum count (`maxi`).

---

### Case 2: Map size exceeds 2
The moment a third unique fruit type enters the window (`HashMap.size() > 2`), our baskets overflow. 

To fix this, we contract the window from the left by moving the pointer `l` forward. As `fruits[l]` exits the window, we decrement its frequency count in our map. If its count reaches `0`, we remove that fruit type completely from the HashMap. We repeat this contraction until the map size drops back down to 2.

---

# Visualization

Tracking pointers on `fruits = [0,1,2,2]`:

```text
1. Initial State: l = 0, r = 0, maxi = 0, hm = {}
2. Process index 0 (fruit 0): Unique. hm = {0: 1}. size <= 2 -> maxi = max(0, 0-0+1) = 1.
3. Process index 1 (fruit 1): Unique. hm = {0: 1, 1: 1}. size <= 2 -> maxi = max(1, 1-0+1) = 2.
4. Process index 2 (fruit 2): Unique! hm = {0: 1, 1: 1, 2: 1}. 
   - Size is now 3 (Invalid!).
   - Shrink from left: l is at 0 (fruit 0). Decrement its count to 0 and remove it.
   - l increments to 1. hm becomes {1: 1, 2: 1}. Valid!
   - Window size = 2 - 1 + 1 = 2. maxi = max(2, 2) = 2.
5. Process index 3 (fruit 2): Existing. hm = {1: 1, 2: 2}. size <= 2 -> maxi = max(2, 3-1+1) = 3.

Final Window Snapshot:
fruits = [0, 1, 2, 2]
            l        r
Max fruits picked = 3.
```

---

# Java Implementation

```java
import java.util.HashMap;

class Solution {
    public int totalFruit(int[] fruits) {
        // HashMap to store the frequencies of at most 2 types of fruits
        HashMap<Integer, Integer> hm = new HashMap<>();
        int l = 0, r = 0;
        int n = fruits.length;
        int maxi = 0;
        
        while (r < n) {
            // Add the current fruit to our window / basket
            hm.put(fruits[r], hm.getOrDefault(fruits[r], 0) + 1);
            
            // If we have more than 2 types of fruits, shrink the window from the left
            while (hm.size() > 2) {
                hm.put(fruits[l], hm.get(fruits[l]) - 1);
                
                // If a fruit type's count drops to 0, completely remove it from the basket
                if (hm.get(fruits[l]) == 0) {
                    hm.remove(fruits[l]);                    
                }
                l++;
            }
            
            // Track the maximum number of fruits collected so far
            maxi = Math.max(maxi, r - l + 1);
            r++;
        }
        
        return maxi;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the total number of trees in the `fruits` array. Even though there is a nested `while` loop, both pointers `l` and `r` only travel through the array from left to right exactly once. Map insertions and deletions take O(1) time.
* **Space Complexity:** O(1) auxiliary space. Since the inner loop forces the HashMap size to never exceed 3, the space required by the map remains constant regardless of the input array size.
