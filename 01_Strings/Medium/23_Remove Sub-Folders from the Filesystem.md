# Remove Sub-Folders from the Filesystem

## Problem Statement

Given a list of folders `folder`, return *the folders after removing all **sub-folders** in those folders*. You may return the answer in **any order**.

If a `folder[i]` is located within another `folder[j]`, it is called a **sub-folder** of it. A folder path is defined as a string of one or more names separated by slashes starting with a slash. For example, `"/a/b"` is a sub-folder of `"/a"`, but `"/a/b/c"` is not a sub-folder of `"/a/b/cd"`.

The overall runtime complexity must be bounded by:

```text
O(n * L * log(n))
```

*(where n is the total number of folder paths and L is the maximum length of a folder path string)*

---

## Examples

### Example 1

**Input**

```java
folder = ["/a","/a/b","/c/d","/c/d/e","/c/f"]
```

**Output**

```java
["/a","/c/d","/c/f"]
```

**Explanation**

- `"/a/b"` is a sub-folder of `"/a"`. It is removed.
- `"/c/d/e"` is a sub-folder of `"/c/d"`. It is removed.
- `"/c/f"` and `"/c/d"` are distinct parent folders. They are kept.

---

### Example 2

**Input**

```java
folder = ["/a","/a/b/c","/a/b/d"]
```

**Output**

```java
["/a"]
```

**Explanation**

Both `"/a/b/c"` and `"/a/b/d"` are nested within `"/a"`.

---

### Example 3

**Input**

```java
folder = ["/a/b/c","/a/b/ca","/a/b/d"]
```

**Output**

```java
["/a/b/c","/a/b/ca","/a/b/d"]
```

**Explanation**

`"/a/b/ca"` is **not** a sub-folder of `"/a/b/c"` because it does not follow a directory slash delimiter path boundary.

---

## Brute Force Approach

Compare every single folder path against all other paths in the array using nested loops, checking if one string serves as a matching prefix followed by a slash delimiter for another.

### Steps

1. Run an outer loop `i` from `0` to `n - 1`.
2. Run an inner loop `j` from `0` to `n - 1`.
3. If `i != j`, check if `folder[i]` is a parent of `folder[j]`. 
4. The structural parent check condition requires: `folder[j].startsWith(folder[i] + "/")`.
5. If a path is verified as a sub-folder, mark it as invalid and filter it out of the final list.

### Complexity

```text
Time Complexity: O(n^2 * L) // due to comparing all pairs of strings
Space Complexity: O(n) to track valid folder states
```

Comparing all directory pairs takes quadratic time, causing standard execution timeouts for massive folder structures. We can optimize this process down to log-linear time by sorting the input array alphabetically.

---

# Optimal Approach: Sorting via Delimiter Lookahead Filtering

## Key Idea

When we sort a list of directory paths **alphabetically**, a parent folder will **always appear immediately before all of its sub-folders**. 

For example, sorting groups them into a predictable sequence layout:
```text
Sorted Array: ["/a", "/a/b", "/a/b/c", "/c/d", "/c/d/e"]
```

This structural property allows us to isolate parental chains in a **single forward pass** by keeping track of the last successfully kept parent folder:
1. Sort the `folder` array lexicographically (alphabetically).
2. Initialize an empty result list `result` and add the absolute first folder `folder[0]` to it as our baseline `parent` tracker.
3. Iterate forward through the sorted folders from index `1` up to `n - 1`:
   - Let the active candidate string be `current`.
   - **The Sub-Folder Boundary Check:** Check if `current` starts with our active `parent` tracking string followed by a slash: `current.startsWith(parent + "/")`.
   - **Case A (Is a Sub-Folder):** If the condition is true, `current` is a nested sub-directory of our active parent. Ignore it completely.
   - **Case B (Is a New Parent Folder):** If the condition is false, `current` is an independent directory. Add `current` straight into `result`, and update our tracking register: `parent = current`.

---

## Visual Understanding

Suppose `folder = ["/a/b", "/a", "/c/d/e", "/c/d"]`:

- **Step 1 (Lexicographical Sort):** `["/a", "/a/b", "/c/d", "/c/d/e"]`
- **Step 2 (Initialize Tracker):** Add `"/a"` to results. Active `parent = "/a"`.
- **Step 3 (Linear Sweep):**
  - **i = 1 (`"/a/b"`):** Does `"/a/b"` start with `"/a" + "/"` (`"/a/"`)? Yes! This is a sub-folder. Skip it.
  - **i = 2 (`"/c/d"`):** Does `"/c/d"` start with `"/a/"`? No. This is a brand new parent track! Add `"/c/d"` to results. Update active tracker: `parent = "/c/d"`.
  - **i = 3 (`"/c/d/e"`):** Does `"/c/d/e"` start with `"/c/d/"`? Yes! This is a sub-folder. Skip it.

The list yields `["/a", "/c/d"]` efficiently in a single pass.

---

## Partition Variables

Let:

```java
Arrays.sort(folder);
List<String> result = new ArrayList<>();
String parent = folder[0];
result.add(parent);
```

---

### Border Elements

Appending the slash delimiter `+ "/"` is absolutely mandatory to prevent false positive matches on folders that share a text prefix but represent completely different directories (e.g., preventing `"/a/b/ca"` from being treated as a sub-folder of `"/a/b/c"`):

```java
if (!f.startsWith(parent + "/")) {
    result.add(f);
    parent = f; // Transition to the new parent domain
}
```

---

## Correct Partition Condition

The log-linear sweep validates prefix boundary alignments within a single forward pass loop:

```java
for (int i = 1; i < folder.length; i++) {
    if (!folder[i].startsWith(parent + "/")) {
        result.add(folder[i]);
        parent = folder[i];
    }
}
```

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

class Solution {

    public List<String> removeSubfolders(String[] folder) {
        if (folder == null || folder.length == 0) {
            return new ArrayList<>();
        }

        // Step 1: Sort the folders alphabetically
        // Alphabetical sorting guarantees that parent folders directly precede their sub-folders
        Arrays.sort(folder);

        List<String> result = new ArrayList<>();
        
        // Step 2: Establish the absolute first directory as our baseline parent tracker
        String parent = folder[0];
        result.add(parent);

        // Step 3: Scan the sorted array from left to right in a single pass
        for (int i = 1; i < folder.length; i++) {
            String currentFolder = folder[i];

            // If the current folder does not start with "parent + /", it is an independent parent folder
            if (!currentFolder.startsWith(parent + "/")) {
                result.add(currentFolder);
                parent = currentFolder; // Shift our parent tracker to the newly discovered root
            }
        }

        return result;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Alphabetical Sorting Pass** | **O(n * L * log(n))** | **O(1)** | Sorting an array of reference strings of length `L` bounds the performance bottleneck to log-linear scale parameters. The subsequent filtering sweep runs in a linear O(n * L) time frame. Auxiliary tracking registers require constant space. |

---

## Key Insight

Sorting directory paths alphabetically groups parent-child sets contiguously, changing a complex nested lookup problem into a single-pass string prefix filter.

```text
Time  : O(n * L * log(n)) sorting performance runtime
Space : O(1) absolute constant auxiliary tracking memory
```

---

## Similar Problems

1. Simplify Path (71)
2. Crawler Log Folder (1598)
3. Longest Common Prefix (14)
4. Group Anagrams (49)
5. Design In-Memory File System (588)
