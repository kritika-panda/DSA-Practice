# How `String.indexOf()` Works Internally in Java

## What is `indexOf()`?

The `indexOf()` method is used to find the first occurrence of a character or substring inside a string.

If the character or substring is found:

```java
Returns the starting index
```

Otherwise:

```java
Returns -1
```

---

# Method Variants

## Find Character

```java
str.indexOf('a')
```

---

## Find Character From a Starting Position

```java
str.indexOf('a', 5)
```

---

## Find Substring

```java
str.indexOf("abc")
```

---

## Find Substring From a Starting Position

```java
str.indexOf("abc", 10)
```

---

# Examples

## Character Search

```java
String str = "banana";

System.out.println(
    str.indexOf('a')
);
```

Output:

```text
1
```

---

## Character Not Present

```java
System.out.println(
    str.indexOf('z')
);
```

Output:

```text
-1
```

---

## Substring Search

```java
String str = "hello world";

System.out.println(
    str.indexOf("world")
);
```

Output:

```text
6
```

---

## Search After Position

```java
String str = "banana";

System.out.println(
    str.indexOf('a', 2)
);
```

Output:

```text
3
```

---

# Internal Working

Consider:

```java
String str = "banana";

str.indexOf('a');
```

Java performs a linear scan.

---

## Step 1

```text
Index 0
Character 'b'
```

Match?

```text
No
```

---

## Step 2

```text
Index 1
Character 'a'
```

Match?

```text
Yes
```

Return:

```text
1
```

---

# Internal Logic for Character Search

Simplified implementation:

```java
public int indexOf(char target) {

    for (int i = 0; i < value.length; i++) {

        if (value[i] == target) {
            return i;
        }
    }

    return -1;
}
```

where:

```java
value
```

is the internal character array.

---

# Internal Working for Substring Search

Consider:

```java
String str = "banana";

str.indexOf("ana");
```

---

## Visual Representation

```text
banana
012345
```

Target:

```text
ana
```

---

### Start at Index 0

```text
banana
ana
```

Mismatch

---

### Start at Index 1

```text
banana
 ana
```

Match:

```text
a == a
n == n
a == a
```

Return:

```text
1
```

---

# Simplified Internal Logic

```java
public int indexOf(String target) {

    int n = text.length();
    int m = target.length();

    for (int i = 0; i <= n - m; i++) {

        int j;

        for (j = 0; j < m; j++) {

            if (text.charAt(i + j) !=
                target.charAt(j)) {
                break;
            }
        }

        if (j == m) {
            return i;
        }
    }

    return -1;
}
```

---

# Example Dry Run

## Input

```java
String text = "mississippi";
String pattern = "iss";
```

---

### i = 0

```text
mississippi
iss
```

Mismatch

---

### i = 1

```text
mississippi
 iss
```

Check:

```text
i == i
s == s
s == s
```

Match found.

Return:

```text
1
```

---

# Time Complexity

## Character Search

```java
str.indexOf('a')
```

Worst Case:

```text
O(n)
```

Example:

```java
"abcdefghijkz"
```

Searching:

```java
'z'
```

Must scan entire string.

---

## Substring Search

```java
str.indexOf("abc")
```

Worst Case:

```text
O(n × m)
```

where:

```text
n = text length
m = pattern length
```

---

# Why Isn't It O(1)?

Because Java may need to inspect multiple characters.

Example:

```java
"aaaaaaaaaaaaaaaab"
```

Searching:

```java
"aaaaaac"
```

Many comparisons are needed.

---

# Current JDK Optimization

Modern JDK implementations contain optimized native-style loops and compact string representations.

Instead of the exact naive implementation shown above, the JVM performs several low-level optimizations:

```text
Fast Character Access
Loop Optimizations
Compact String Handling
Byte Array Optimizations
```

However, from an interview perspective, you should think of:

```text
Character Search → O(n)

Substring Search → O(n × m)
```

---

# indexOf() vs contains()

## contains()

```java
str.contains("abc")
```

Returns:

```java
true / false
```

Implementation:

```java
return indexOf(...) >= 0;
```

Example:

```java
"banana".contains("ana")
```

Output:

```text
true
```

---

## indexOf()

Returns:

```java
Actual Position
```

Example:

```java
"banana".indexOf("ana")
```

Output:

```text
1
```

---

# Finding Multiple Occurrences

Example:

```java
String str = "banana";

int idx =
    str.indexOf('a');
```

First occurrence:

```text
1
```

---

### Next Occurrence

```java
idx = str.indexOf('a', idx + 1);
```

Output:

```text
3
```

---

### Next Occurrence

```java
idx = str.indexOf('a', idx + 1);
```

Output:

```text
5
```

---

# Last Occurrence

Use:

```java
lastIndexOf()
```

Example:

```java
String str = "banana";

System.out.println(
    str.lastIndexOf('a')
);
```

Output:

```text
5
```

---

# Difference Between `indexOf()` and `lastIndexOf()`

## indexOf()

Searches:

```text
Left → Right
```

Example:

```java
"banana".indexOf('a')
```

Output:

```text
1
```

---

## lastIndexOf()

Searches:

```text
Right → Left
```

Example:

```java
"banana".lastIndexOf('a')
```

Output:

```text
5
```

---

# Common Interview Applications

## Check Character Exists

```java
if(str.indexOf(ch) != -1)
```

---

## Count Vowels

```java
if("aeiou".indexOf(ch) != -1)
```

---

## Check Vowel

```java
boolean vowel =
    "aeiouAEIOU".indexOf(ch) != -1;
```

---

## Find Repeated Character

```java
if(str.indexOf(ch) != str.lastIndexOf(ch))
```

---

## String Search

```java
text.indexOf(pattern)
```

---

# Complexity Summary

| Operation | Time Complexity |
|------------|------------|
| indexOf(char) | O(n) |
| indexOf(String) | O(n × m) |
| lastIndexOf(char) | O(n) |
| lastIndexOf(String) | O(n × m) |
| contains() | O(n × m) |

---

# Interview Cheatsheet

## Find Character

```java
str.indexOf('a');
```

---

## Find Substring

```java
str.indexOf("abc");
```

---

## Find From Position

```java
str.indexOf('a', startIndex);
```

---

## Last Occurrence

```java
str.lastIndexOf('a');
```

---

## Contains Check

```java
str.indexOf(ch) != -1
```

---

## Vowel Check

```java
"aeiou".indexOf(ch) != -1
```

---

# Key Takeaways

```text
indexOf()
=
Linear Search
```

For a character:

```text
Scan characters one by one.
```

For a substring:

```text
Try matching the pattern at every possible position.
```

Complexities:

```text
indexOf(char)     → O(n)

indexOf(String)   → O(n × m)

lastIndexOf()     → O(n)

contains()        → Uses indexOf internally
```

Understanding `indexOf()` is important for:

```text
✓ String Matching
✓ Sliding Window
✓ Hashing
✓ Trie
✓ Pattern Searching
✓ FAANG Interviews
```
