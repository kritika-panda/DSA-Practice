# Simplify Path

## Problem Statement

Given an absolute path for a Unix-style file system, which begins with a slash `'/'`, transform it into the **simplified canonical path**.

In a Unix-style file system, the formatting rules are as follows:
* A single period `'.'` refers to the **current directory**.
* A double period `'..'` moves the directory up a level to the **parent directory**.
* Multiple sequential slashes (e.g., `"//"` or `"///"`) are treated as a **single slash** `'/'`.
* Any other format of periods (such as `Related...`) are treated as **valid directory names**.

The simplified canonical path must follow these strict style formats:
1. The path must start with a single slash `'/'`.
2. Any two directories must be separated by a single slash `'/'`.
3. The path must not end with a trailing slash `'/'` unless it is the root directory itself.
4. The path must only contain the directories on the path from the root directory to the target directory (i.e., no conditional `'.'` or `'..'`).

The overall runtime complexity must be strictly bounded by:

```text
O(n)
```

*(where n is the length of the input path string)*

---

## Examples

### Example 1

**Input**

```java
path = "/home/"
```

**Output**

```java
"/home"
```

**Explanation**

The trailing slash is removed.

---

### Example 2

**Input**

```java
path = "/home//foo/"
```

**Output**

```java
"/home/foo"
```

**Explanation**

Multiple sequential slashes are reduced to a single slash, and the trailing slash is stripped.

---

### Example 3

**Input**

```java
path = "/home/user/Documents/../Pictures"
```

**Output**

```java
"/home/user/Pictures"
```

**Explanation**

The double period `'..'` moves the pointer up one level from `"Documents"` to its parent directory `"Documents"`, then steps into `"Pictures"`.

---

### Example 4

**Input**

```java
path = "/../"
```

**Output**

```java
"/"
```

**Explanation**

Moving up a level from the root directory is a no-op; you remain at the root folder.

---

### Example 5

**Input**

```java
path = "/.../b/../c"
```

**Output**

```java
"/..."
```

**Explanation**

`"..."` is treated as a valid directory name here. Moving up one level from `"b"` returns to `"..."`, then steps into `"c"`.

---

## Brute Force Approach

Iterate through the characters of the string sequentially, building up segments and using manual forward/backward pointer index calculations to shift markers or prune character blocks within a mutable string buffer.

### Steps

1. Clean sequential slashes from the string by repeatedly searching for `"//"` and replacing it with `"/"`.
2. Look for patterns matching `"/\./"` and strip them out.
3. Handle parent backtracking by looking for `"/[dir]/../"` fragments, calculating the lengths of those substrings, and excising them from the string.
4. Ensure the string starts with a single slash and contains no trailing slashes.

### Complexity

```text
Time Complexity: O(n^2) // due to repeatedly scanning, slicing, and shifting characters inside string buffers
Space Complexity: O(n) to store the intermediate mutated strings
```

Repeatedly slicing and shifting characters inside text buffers creates heavy quadratic performance traps. Since directories open and collapse in a strict Last-In, First-Out (LIFO) pattern, we can optimize this process down to a clean linear pass using a **Stack**.

---

# Optimal Approach: String Tokenization with Stack Tracking

## Key Idea

Instead of evaluating individual characters or doing recursive slice operations, we can split the path by the slash separator character `'/'`. This tokenizes the path into clear, distinct directory commands.

We track the current structural directory framework using a **Stack**:
1. **Tokenization Pass:** Split the string `path` by the slash delimiter: `path.split("/")`. This extracts a clean sequence of names, completely filtering out and neutralizing sequential slash segments (`"//"` evaluates to empty tokens `""`).
2. **LIFO Command Processing:** Initialize a `Deque<String>` to act as our directory stack. Iterate forward through the token segments:
   - **Case A (Empty `""` or Current Directory `"."`):** Ignore the token completely and move to the next item.
   - **Case B (Parent Directory `".."`):** We need to backtrack. If our stack is not empty, pop the top directory token off to step up a level. If the stack is empty, it means we are already at the root directory—do nothing.
   - **Case C (Valid Name):** The token represents entering a new child directory (e.g., `"home"`, `"..."`). Push this token directly onto our stack.
3. **Canonical Assembly:** If the stack is completely empty, return the root directory `"/"`. Otherwise, loop through the stack elements from bottom to top, prepending a slash `'/'` before each directory name, and return the combined text.

---

## Visual Understanding

Suppose `path = "/home/user/../Pictures/"`:

- **Token Arrays:** `["", "home", "user", "..", "Pictures", ""]`
- **Processing Tokens:**
  - `""` \(\implies\) Skip.
  - `"home"` \(\implies\) Valid directory. Push. Stack = `["home"]`.
  - `"user"` \(\implies\) Valid directory. Push. Stack = `["home", "user"]`.
  - `".."` \(\implies\) Parent backtrack command. Pop top element `"user"`. Stack = `["home"]`.
  - `"Pictures"` \(\implies\) Valid directory. Push. Stack = `["home", "Pictures"]`.
  - `""` \(\implies\) Skip.
- **Assembly Phase:** Read elements from bottom to top: Append `"/" + "home"` followed by `"/" + "Pictures"`.

The final canonical output `"home"` matches expectations precisely.

---

## Partition Variables

Let:

```java
String[] components = path.split("/");
Deque<String> stack = new ArrayDeque<>();
StringBuilder sb = new StringBuilder();
```

---

### Border Elements

When rebuilding the final path sequence, reading elements from bottom to top is essential to preserve the original parent-to-child sequence. Java's `ArrayDeque` supports this natively via `.removeLast()` or iterating forward through its standard iterator collection:

```java
while (!stack.isEmpty()) {
    sb.append("/").append(stack.removeLast()); // Pulls from the bottom of the stack up to the top
}
```

---

## Correct Partition Condition

The token tracking rules filter out empty parameters and direct backtrack triggers cleanly inside a single loop sweep:

```java
for (String dir : components) {
    if (dir.equals("..")) {
        if (!stack.isEmpty()) stack.pop();
    } else if (!dir.isEmpty() && !dir.equals(".")) {
        stack.push(dir);
    }
}
```

---

## Java Solution

```java
import java.util.ArrayDeque;
import java.util.Deque;

class Solution {

    public String simplifyPath(String path) {
        if (path == null || path.length() == 0) {
            return "/";
        }

        // Step 1: Split the path into tokens by the slash separator '/'
        // Sequential slashes (e.g., "//") naturally turn into empty strings "" which we can skip
        String[] components = path.split("/");
        
        // Deque is heavily preferred over the legacy Stack class for single-thread operations
        Deque<String> stack = new ArrayDeque<>();

        // Step 2: Process each token component sequentially
        for (String dir : components) {
            // If the token is a double period "..", move up one directory level if possible
            if (dir.equals("..")) {
                if (!stack.isEmpty()) {
                    stack.pop();
                }
            } 
            // Skip empty tokens and single period "." current directory indicators
            else if (!dir.isEmpty() && !dir.equals(".")) {
                stack.push(dir); // Found a valid directory name; push onto the stack
            }
        }

        // Step 3: Reconstruct the canonical path from the remaining stack elements
        if (stack.isEmpty()) {
            return "/";
        }

        StringBuilder sb = new StringBuilder();
        // ArrayDeque iterates from top (most recent) to bottom (oldest). 
        // To build the path from root down, we extract items from the bottom up.
        while (!stack.isEmpty()) {
            sb.append("/").append(stack.removeLast());
        }

        return sb.toString();
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Token Stack Processing** | **O(n)** | **O(n)** | Splitting the path string takes linear O(n) time, and the tracking loop evaluates each token once. The stack holds up to `n` unique elements matching input length limitations. |

---

## Key Insight

Tokenizing string inputs by slashes and processing directory segments inside a strict LIFO stack avoids complex substring index calculation traps, building canonical Unix tracks inside true linear boundaries.

```text
Time  : O(n) linear performance tracking pass
Space : O(n) structural stack and token array allocations
```

---

## Similar Problems

1. Crawler Log Folder (1598)
2. Valid Parentheses (20)
3. Decode String (394)
4. Remove Sub-Folders from the Filesystem (1233)
5. Backspace String Compare (844)
