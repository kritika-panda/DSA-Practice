# Intersection of Two Sorted Arrays

## Problem Statement

Given two sorted arrays `a[]` and `b[]`, return their intersection.

The intersection contains all common elements present in both arrays. Duplicate elements should appear only once in the result.

### Example 1

**Input:**
```java
a = [1, 2, 2, 3, 4]
b = [2, 2, 4, 6]
```

**Output:**
```java
[2, 4]
```

### Example 2

**Input:**
```java
a = [1, 2, 3, 4, 5]
b = [2, 4, 6, 8]
```

**Output:**
```java
[2, 4]
```

---

## Intuition

Since both arrays are sorted:

- If `a[i] < b[j]`, move `i`.
- If `a[i] > b[j]`, move `j`.
- If both are equal, add the element to the result and move both pointers.

To avoid duplicates, only add an element if it is different from the last inserted element.

---

## Approach (Two Pointers)

1. Initialize two pointers:
   - `i = 0` for array `a`
   - `j = 0` for array `b`
2. Compare elements:
   - If `a[i] < b[j]`, increment `i`.
   - If `a[i] > b[j]`, increment `j`.
   - Otherwise:
     - Add the element to the answer if not already added.
     - Move both pointers.
3. Continue until one array is exhausted.

---

## Dry Run

### Input

```java
a = [1, 2, 2, 3, 4]
b = [2, 2, 4, 6]
```

| i | j | a[i] | b[j] | Result |
|---|---|------|------|---------|
| 0 | 0 | 1 | 2 | [] |
| 1 | 0 | 2 | 2 | [2] |
| 2 | 1 | 2 | 2 | [2] |
| 3 | 2 | 3 | 4 | [2] |
| 4 | 2 | 4 | 4 | [2,4] |

### Final Output

```java
[2,4]
```

---

## Java Solution (Optimal)

```java
import java.util.*;

class Solution {
    public static ArrayList<Integer> intersection(int[] a, int[] b) {
        int i = 0, j = 0;

        ArrayList<Integer> result = new ArrayList<>();

        while (i < a.length && j < b.length) {

            if (a[i] < b[j]) {
                i++;
            } else if (a[i] > b[j]) {
                j++;
            } else {

                if (result.isEmpty() ||
                    result.get(result.size() - 1) != a[i]) {
                    result.add(a[i]);
                }

                i++;
                j++;
            }
        }

        return result;
    }
}
```

---

## Alternative Approach (HashSet)

### Idea

- Store all elements of the first array in a set.
- Traverse the second array.
- If an element exists in the set, add it to the answer set.
- Convert the answer set to a list.

### Java Code

```java
import java.util.*;

class Solution {
    public static ArrayList<Integer> intersection(int[] a, int[] b) {

        HashSet<Integer> set = new HashSet<>();
        HashSet<Integer> ans = new HashSet<>();

        for (int num : a) {
            set.add(num);
        }

        for (int num : b) {
            if (set.contains(num)) {
                ans.add(num);
            }
        }

        return new ArrayList<>(ans);
    }
}
```

---

## Complexity Analysis

### Two Pointer Approach

- **Time Complexity:** `O(n + m)`
- **Space Complexity:** `O(1)` (excluding result array)

### HashSet Approach

- **Time Complexity:** `O(n + m)`
- **Space Complexity:** `O(n)`

---

## Why Two Pointer Approach is Optimal?

Because the arrays are already sorted:

- No extra data structure is needed.
- Each element is visited at most once.
- Uses constant auxiliary space.

---

## Variation: Intersection Including Duplicates

### Example

```java
a = [1,2,2,2,3]
b = [2,2,4]
```

### Output

```java
[2,2]
```

### Java Code

```java
import java.util.*;

class Solution {
    public static ArrayList<Integer> intersection(int[] a, int[] b) {
        int i = 0, j = 0;

        ArrayList<Integer> result = new ArrayList<>();

        while (i < a.length && j < b.length) {

            if (a[i] < b[j]) {
                i++;
            } else if (a[i] > b[j]) {
                j++;
            } else {
                result.add(a[i]);
                i++;
                j++;
            }
        }

        return result;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** `O(n + m)`
- **Space Complexity:** `O(1)` (excluding result)

---

## Key Takeaways

- Sorted arrays immediately suggest the **Two Pointer Technique**.
- Move the pointer pointing to the smaller element.
- When elements are equal, add to the result and move both pointers.
- Avoid duplicates by checking the last added element.
- Optimal complexity is **O(n + m)** time and **O(1)** extra space.
- Very common interview problem and a foundation for merge-based array questions.
