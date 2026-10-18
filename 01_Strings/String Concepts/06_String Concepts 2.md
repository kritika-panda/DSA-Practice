# All String Operations in Java

## What Is a String in Java?

In Java, a `String` is an **immutable sequence of characters** represented internally as a `char[]` array (prior to Java 9) or a `byte[]` array with an encoding flag (Java 9+ compact strings). The `String` class lives in `java.lang` and is automatically imported into every Java file.

**Immutability** is the defining property of Java strings:

> Every operation that appears to "modify" a String actually creates and returns a **brand-new String object**. The original is never altered.

```java
String s = "hello";
s.toUpperCase();        // Does NOT modify s
System.out.println(s);  // Still prints: hello

String result = s.toUpperCase();
System.out.println(result); // Prints: HELLO
```

---

## The String Pool

Java maintains a special memory region called the **String Constant Pool** (inside the heap since Java 7) to deduplicate string literals:

```java
String a = "java";   // Stored in the pool
String b = "java";   // Reuses the same pool object
String c = new String("java"); // Forces a new heap object — bypasses pool

System.out.println(a == b);      // true  — same pool reference
System.out.println(a == c);      // false — different heap address
System.out.println(a.equals(c)); // true  — same character content

// intern() moves a heap string back into the pool
String d = c.intern();
System.out.println(a == d);      // true
```

> **Rule:** Always use `.equals()` to compare string **content**. Never use `==` for string comparison in production code.

---

# Section 1 — String Creation

```java
// 1. String literal (uses pool)
String s1 = "Hello, World!";

// 2. new keyword (always creates a new heap object)
String s2 = new String("Hello, World!");

// 3. From a char array
char[] chars = {'J', 'a', 'v', 'a'};
String s3 = new String(chars);                  // "Java"
String s4 = new String(chars, 1, 2);            // "av" (offset=1, count=2)

// 4. From a byte array (with charset)
byte[] bytes = {72, 101, 108, 108, 111};
String s5 = new String(bytes);                  // "Hello"
String s6 = new String(bytes, "UTF-8");         // "Hello" with explicit encoding

// 5. From StringBuilder or StringBuffer
StringBuilder sb = new StringBuilder("build");
String s7 = sb.toString();                      // "build"

// 6. valueOf — converts primitives to String
String s8  = String.valueOf(42);         // "42"
String s9  = String.valueOf(3.14);       // "3.14"
String s10 = String.valueOf(true);       // "true"
String s11 = String.valueOf('A');        // "A"
String s12 = String.valueOf(chars);      // "Java"

// 7. Concatenation literal (compile-time constant folding)
String s13 = "Hello" + ", " + "World!"; // "Hello, World!"
```

---

# Section 2 — Inspection and Length

## `length()`

Returns the number of characters in the string.

```java
String s = "Java";
System.out.println(s.length()); // 4

String empty = "";
System.out.println(empty.length()); // 0
```

**Signature:** `int length()`

---

## `isEmpty()`

Returns `true` if the string has length zero.

```java
System.out.println("".isEmpty());      // true
System.out.println("  ".isEmpty());    // false — spaces are characters
System.out.println("Java".isEmpty());  // false
```

**Signature:** `boolean isEmpty()`

---

## `isBlank()` *(Java 11+)*

Returns `true` if the string is empty or contains only whitespace characters.

```java
System.out.println("".isBlank());      // true
System.out.println("  ".isBlank());    // true
System.out.println(" A ".isBlank());   // false
```

**Signature:** `boolean isBlank()`

---

## `charAt(int index)`

Returns the `char` at the specified zero-based index.

```java
String s = "Java";
System.out.println(s.charAt(0)); // 'J'
System.out.println(s.charAt(3)); // 'a'
// s.charAt(4) → StringIndexOutOfBoundsException
```

**Signature:** `char charAt(int index)`

---

## `codePointAt(int index)`

Returns the Unicode code point (integer) of the character at the given index. Handles supplementary (surrogate pair) characters beyond the Basic Multilingual Plane.

```java
String s = "A";
System.out.println(s.codePointAt(0)); // 65
```

**Signature:** `int codePointAt(int index)`

---

## `toCharArray()`

Converts the string into a new `char[]` array — the most common way to iterate character by character in algorithm problems.

```java
String s = "hello";
char[] arr = s.toCharArray();
// arr = ['h', 'e', 'l', 'l', 'o']

for (char c : arr) {
    System.out.print(c + " "); // h e l l o
}

// Modify and convert back
arr[0] = 'H';
String modified = new String(arr); // "Hello"
```

**Signature:** `char[] toCharArray()`

---

## `getBytes()`

Returns the string encoded into a sequence of bytes using the platform's default charset, or a specified charset.

```java
String s = "Hello";
byte[] bytes = s.getBytes();               // Default charset
byte[] utf8  = s.getBytes("UTF-8");        // Explicit UTF-8 encoding
byte[] utf16 = s.getBytes("UTF-16");       // Explicit UTF-16 encoding
```

**Signature:** `byte[] getBytes()` / `byte[] getBytes(String charsetName)`

---

# Section 3 — String Comparison

## `equals(Object obj)`

Compares string **content** character by character. Case-sensitive.

```java
String a = "Java";
String b = "Java";
String c = "java";

System.out.println(a.equals(b)); // true
System.out.println(a.equals(c)); // false — case differs
System.out.println(a.equals(null)); // false — null-safe, no exception
```

**Signature:** `boolean equals(Object anObject)`

---

## `equalsIgnoreCase(String other)`

Compares content case-insensitively.

```java
System.out.println("Java".equalsIgnoreCase("java")); // true
System.out.println("Java".equalsIgnoreCase("JAVA")); // true
System.out.println("Java".equalsIgnoreCase("Kotlin")); // false
```

**Signature:** `boolean equalsIgnoreCase(String anotherString)`

---

## `compareTo(String other)`

Lexicographic comparison. Returns:
- `0` if strings are equal
- **negative** if this string comes before `other`
- **positive** if this string comes after `other`

```java
System.out.println("apple".compareTo("banana")); // negative (a < b)
System.out.println("banana".compareTo("apple")); // positive (b > a)
System.out.println("apple".compareTo("apple"));  // 0
System.out.println("apple".compareTo("app"));    // 2 (length diff: 5 - 3)
```

**Signature:** `int compareTo(String anotherString)`

# Java String `compareTo()` Deep Dive

The `compareTo()` method compares two strings **lexicographically** (dictionary order) based on the Unicode value of each character, proceeding from left to right.

---

# Example 1

## Code

```java
System.out.println("Apple".compareTo("apple"));
```

## Output

```java
-32
```

---

## Step-by-Step Explanation

Java compares both strings character by character.

### Comparison

```java
"Apple"
"apple"
```

Compare first characters:

```java
'A' vs 'a'
```

ASCII/Unicode values:

```java
'A' = 65
'a' = 97
```

Calculation:

```java
65 - 97 = -32
```

Since the first characters are different, Java immediately stops the comparison and returns:

```java
-32
```

---

## Visualization

```text
Apple
^

apple
^

'A' = 65
'a' = 97

65 - 97 = -32
```

---

## Key Takeaway

```java
"Apple".compareTo("apple")
```

returns:

```java
-32
```

because uppercase letters have smaller ASCII values than lowercase letters.

---

# Example 2

## Code

```java
System.out.println("Apple".compareTo("App"));
```

## Output

```java
2
```

---

## Step-by-Step Explanation

Java compares characters one by one.

### Matching Characters

```java
A == A
p == p
p == p
```

At this point:

```java
"App"
```

has ended.

No mismatching character was found.

---

## What Happens Next?

When one string is a complete prefix of the other:

```java
"App"
```

is a prefix of

```java
"Apple"
```

Java returns:

```java
Length of First String - Length of Second String
```

Calculation:

```java
"Apple".length() - "App".length()
```

```java
5 - 3 = 2
```

Hence:

```java
"Apple".compareTo("App")
```

returns:

```java
2
```

---

## Visualization

```text
Apple
|||
App

All compared characters are equal.

Lengths:

Apple = 5
App   = 3

5 - 3 = 2
```

---

# Reverse Comparison

## Code

```java
System.out.println("App".compareTo("Apple"));
```

## Output

```java
-2
```

### Why?

```java
3 - 5 = -2
```

Since the first string is shorter:

```java
"App".compareTo("Apple") = -2
```

---

# General Rules of `compareTo()`

## Rule 1: First Different Character Wins

```java
"Cat".compareTo("Bat")
```

Comparison:

```java
'C' = 67
'B' = 66
```

Result:

```java
67 - 66 = 1
```

Output:

```java
1
```

---

## Rule 2: Prefix Case

```java
"Java".compareTo("Ja")
```

Result:

```java
4 - 2 = 2
```

Output:

```java
2
```

---

## Rule 3: Equal Strings

```java
"Hello".compareTo("Hello")
```

Output:

```java
0
```

because every character matches and lengths are equal.

---

# compareToIgnoreCase()

## Code

```java
System.out.println("Apple".compareToIgnoreCase("apple"));
```

## Output

```java
0
```

### Why?

`compareToIgnoreCase()` converts both characters to the same case during comparison.

```java
Apple
apple
```

becomes effectively:

```java
apple
apple
```

Therefore:

```java
0
```

is returned.

---

# Common Interview Examples

```java
"A".compareTo("a")          // -32
```

```java
"a".compareTo("A")          // 32
```

```java
"abc".compareTo("abd")      // -1
```

```java
"abd".compareTo("abc")      // 1
```

```java
"abc".compareTo("abc")      // 0
```

```java
"abc".compareTo("ab")       // 1
```

```java
"ab".compareTo("abc")       // -1
```

---

# How Sorting Uses `compareTo()`

Consider:

```java
List<String> list =
    Arrays.asList("apple", "Apple", "banana");

Collections.sort(list);

System.out.println(list);
```

Output:

```java
[Apple, apple, banana]
```

Why?

```java
'A' = 65
'a' = 97
```

Since:

```java
65 < 97
```

`"Apple"` comes before `"apple"` when sorted.

---

# Key Takeaways

- `compareTo()` compares strings character by character.
- The comparison stops at the first mismatching character.
- It returns:

```java
ASCII(Character1) - ASCII(Character2)
```

for the first mismatch.

- If one string is a prefix of another:

```java
return length1 - length2;
```

- Examples:

```java
"Apple".compareTo("apple") = -32
```

```java
"Apple".compareTo("App") = 2
```

```java
"App".compareTo("Apple") = -2
```

- `compareToIgnoreCase()` ignores uppercase/lowercase differences.
- Java's default string sorting relies on `compareTo()`.

---

## `compareToIgnoreCase(String other)`

Lexicographic comparison ignoring case differences.

```java
System.out.println("Apple".compareToIgnoreCase("apple")); // 0
System.out.println("apple".compareToIgnoreCase("Banana")); // negative
```

**Signature:** `int compareToIgnoreCase(String str)`

---

## `contentEquals(CharSequence cs)`

Compares the string's content with any `CharSequence` (including `StringBuilder`, `StringBuffer`).

```java
StringBuilder sb = new StringBuilder("hello");
System.out.println("hello".contentEquals(sb)); // true
```

**Signature:** `boolean contentEquals(CharSequence cs)`

---

## `regionMatches(int tOffset, String other, int oOffset, int len)`

Compares a specific region (substring window) of two strings.

```java
String s = "Hello World";
String t = "World Peace";
// Compare s[6..10] with t[0..4]
System.out.println(s.regionMatches(6, t, 0, 5));       // true  ("World" == "World")
System.out.println(s.regionMatches(true, 6, t, 0, 5)); // true  (case-insensitive flag)
```

**Signature:** `boolean regionMatches(boolean ignoreCase, int toffset, String other, int ooffset, int len)`

---

# Section 4 — Searching and Finding

## `indexOf(int ch)`

Returns the index of the **first** occurrence of a character. Returns `-1` if not found.

```java
String s = "banana";
System.out.println(s.indexOf('a'));     // 1
System.out.println(s.indexOf('a', 2)); // 3 — search from index 2
System.out.println(s.indexOf('z'));     // -1
```

**Signature:** `int indexOf(int ch)` / `int indexOf(int ch, int fromIndex)`

---

## `indexOf(String str)`

Returns the index of the **first** occurrence of a substring.

```java
String s = "Hello World Hello";
System.out.println(s.indexOf("Hello"));     // 0
System.out.println(s.indexOf("Hello", 1)); // 12 — search from index 1
System.out.println(s.indexOf("Java"));     // -1
```

**Signature:** `int indexOf(String str)` / `int indexOf(String str, int fromIndex)`

---

## `lastIndexOf(int ch)` / `lastIndexOf(String str)`

Returns the index of the **last** occurrence of a character or substring.

```java
String s = "banana";
System.out.println(s.lastIndexOf('a'));    // 5
System.out.println(s.lastIndexOf('a', 4)); // 3 — search backwards from index 4
System.out.println(s.lastIndexOf("an"));  // 3
```

**Signature:** `int lastIndexOf(int ch)` / `int lastIndexOf(String str)`

---

## `contains(CharSequence s)`

Returns `true` if the string contains the given sequence as a substring.

```java
String s = "Hello World";
System.out.println(s.contains("World")); // true
System.out.println(s.contains("world")); // false — case-sensitive
System.out.println(s.contains(""));     // true — empty string always contained
```

**Signature:** `boolean contains(CharSequence s)`

---

## `startsWith(String prefix)`

Returns `true` if the string begins with the given prefix.

```java
String s = "Hello World";
System.out.println(s.startsWith("Hello")); // true
System.out.println(s.startsWith("World")); // false
System.out.println(s.startsWith("World", 6)); // true — check from offset 6
```

**Signature:** `boolean startsWith(String prefix)` / `boolean startsWith(String prefix, int toffset)`

---

## `endsWith(String suffix)`

Returns `true` if the string ends with the given suffix.

```java
String s = "Hello World";
System.out.println(s.endsWith("World")); // true
System.out.println(s.endsWith("Hello")); // false
```

**Signature:** `boolean endsWith(String suffix)`

---

## `matches(String regex)`

Returns `true` if the **entire string** matches the given regular expression pattern.

```java
System.out.println("12345".matches("\\d+"));        // true  — all digits
System.out.println("abc123".matches("[a-z]+"));     // false — contains digits
System.out.println("hello".matches("[a-zA-Z]+"));   // true
System.out.println("2024-01-15".matches("\\d{4}-\\d{2}-\\d{2}")); // true
```

**Signature:** `boolean matches(String regex)`

---

# Section 5 — Extraction

## `substring(int beginIndex)`

Returns a new string from `beginIndex` to the end.

```java
String s = "Hello World";
System.out.println(s.substring(6)); // "World"
System.out.println(s.substring(0)); // "Hello World"
```

**Signature:** `String substring(int beginIndex)`

---

## `substring(int beginIndex, int endIndex)`

Returns a new string from `beginIndex` (inclusive) to `endIndex` (exclusive).

```java
String s = "Hello World";
System.out.println(s.substring(0, 5)); // "Hello"
System.out.println(s.substring(6, 11)); // "World"
System.out.println(s.substring(3, 3));  // "" — empty string when begin == end
```

**Signature:** `String substring(int beginIndex, int endIndex)`

> **Index Rule:** `0 <= beginIndex <= endIndex <= length()`. Violating this throws `StringIndexOutOfBoundsException`.

---

## `split(String regex)`

Splits the string around matches of the given regular expression, returning a `String[]`.

```java
String s = "one,two,three,four";
String[] parts = s.split(",");
// parts = ["one", "two", "three", "four"]

// Split with a limit — at most 3 tokens
String[] limited = s.split(",", 3);
// limited = ["one", "two", "three,four"]

// Split on whitespace
String sentence = "Hello   World  Java";
String[] words = sentence.split("\\s+"); // ["Hello", "World", "Java"]

// Split on literal dot (must escape in regex)
String path = "a.b.c";
String[] segments = path.split("\\."); // ["a", "b", "c"]
```

**Signature:** `String[] split(String regex)` / `String[] split(String regex, int limit)`

> **Trailing empty strings:** `split` without a limit drops trailing empty strings. Use `split(regex, -1)` to preserve them.

---

## `chars()` *(Java 8+)*

Returns an `IntStream` of character code points — useful for functional-style character processing.

```java
String s = "hello";
s.chars()
 .filter(c -> c == 'l')
 .count(); // 2

// Collect characters back to a list
List<Character> list = s.chars()
    .mapToObj(c -> (char) c)
    .collect(Collectors.toList()); // [h, e, l, l, o]
```

**Signature:** `IntStream chars()`

---

# Section 6 — Transformation

## `toLowerCase()` / `toUpperCase()`

Converts all characters to lower or upper case. The locale-aware overload is preferred for internationalized text.

```java
String s = "Hello World";
System.out.println(s.toLowerCase());            // "hello world"
System.out.println(s.toUpperCase());            // "HELLO WORLD"
System.out.println(s.toLowerCase(Locale.ROOT)); // "hello world" (locale-safe)
```

**Signature:** `String toLowerCase()` / `String toUpperCase()` / `String toLowerCase(Locale locale)`

---

## `trim()`

Removes **leading and trailing** ASCII whitespace (characters with code point ≤ `'\u0020'`).

```java
String s = "   Hello World   ";
System.out.println(s.trim());         // "Hello World"
System.out.println(s.trim().length()); // 11
```

**Signature:** `String trim()`

---

## `strip()` / `stripLeading()` / `stripTrailing()` *(Java 11+)*

Unicode-aware whitespace removal. Handles all Unicode whitespace characters (not just ASCII ≤ `'\u0020'`).

```java
String s = "  Hello World  ";
System.out.println(s.strip());          // "Hello World"
System.out.println(s.stripLeading());   // "Hello World  "
System.out.println(s.stripTrailing());  // "  Hello World"
```

**Signature:** `String strip()` / `String stripLeading()` / `String stripTrailing()`

> **Prefer `strip()` over `trim()`** in modern Java for full Unicode correctness.

---

## `replace(char oldChar, char newChar)`

Replaces **all** occurrences of a character with another character.

```java
String s = "banana";
System.out.println(s.replace('a', 'o')); // "bonono"
```

**Signature:** `String replace(char oldChar, char newChar)`

---

## `replace(CharSequence target, CharSequence replacement)`

Replaces **all** occurrences of a literal substring with a replacement. No regex interpretation.

```java
String s = "Hello World World";
System.out.println(s.replace("World", "Java")); // "Hello Java Java"
System.out.println(s.replace(".", "!"));         // "Hello World World" — literal dot, not regex
```

**Signature:** `String replace(CharSequence target, CharSequence replacement)`

---

## `replaceAll(String regex, String replacement)`

Replaces **all** substrings matching a **regular expression** with a replacement string.

```java
String s = "abc 123 def 456";
System.out.println(s.replaceAll("\\d+", "#")); // "abc # def #"
System.out.println(s.replaceAll("[aeiou]", "*")); // "*bc 123 d*f 456"
System.out.println("  hello   world  ".replaceAll("\\s+", " ").trim()); // "hello world"
```

**Signature:** `String replaceAll(String regex, String replacement)`

---

## `replaceFirst(String regex, String replacement)`

Replaces only the **first** substring matching the regex.

```java
String s = "cat bat sat";
System.out.println(s.replaceFirst("[a-z]at", "dog")); // "dog bat sat"
```

**Signature:** `String replaceFirst(String regex, String replacement)`

---

## `concat(String str)`

Appends the given string to the end. Equivalent to `+` for strings, but `concat` throws `NullPointerException` if `str` is `null`.

```java
String s = "Hello";
System.out.println(s.concat(", World!")); // "Hello, World!"
// s is unchanged — immutability
```

**Signature:** `String concat(String str)`

---

## `repeat(int count)` *(Java 11+)*

Returns a string made by repeating this string `count` times.

```java
System.out.println("ab".repeat(3));  // "ababab"
System.out.println("-".repeat(20));  // "--------------------"
System.out.println("x".repeat(0));   // ""
```

**Signature:** `String repeat(int count)`

---

## `intern()`

Returns the canonical representation of the string from the String Pool. Useful for memory deduplication of dynamically created strings.

```java
String s = new String("hello");
String interned = s.intern();
String literal = "hello";

System.out.println(interned == literal); // true — same pool reference
```

**Signature:** `String intern()`

---

# Section 7 — Joining and Formatting

## `String.join(CharSequence delimiter, CharSequence... elements)`

Joins multiple strings (or any `CharSequence`) with a delimiter.

```java
String result = String.join(", ", "Java", "Python", "Kotlin");
System.out.println(result); // "Java, Python, Kotlin"

List<String> list = Arrays.asList("one", "two", "three");
System.out.println(String.join(" | ", list)); // "one | two | three"

System.out.println(String.join("-", "2024", "01", "15")); // "2024-01-15"
```

**Signature:** `static String join(CharSequence delimiter, CharSequence... elements)` / `static String join(CharSequence delimiter, Iterable<? extends CharSequence> elements)`

---

## `String.format(String format, Object... args)`

Produces a formatted string using format specifiers — Java's equivalent of `printf`.

```java
String name = "Alice";
int age = 30;
double gpa = 3.857;

System.out.println(String.format("Name: %s, Age: %d", name, age));
// "Name: Alice, Age: 30"

System.out.println(String.format("GPA: %.2f", gpa));
// "GPA: 3.86"

System.out.println(String.format("%-10s | %5d", name, age));
// "Alice      |    30"  (left-align name, right-align age)

System.out.println(String.format("%08.3f", gpa));
// "0003.857" (zero-padded, 8 total width)
```

**Common Format Specifiers:**

| Specifier | Type | Example |
|---|---|---|
| `%s` | String | `"hello"` |
| `%d` | Integer | `42` |
| `%f` | Float/Double | `3.140000` |
| `%.2f` | Float (2 decimals) | `3.14` |
| `%c` | Character | `'A'` |
| `%b` | Boolean | `true` |
| `%n` | Newline (platform-safe) | |
| `%x` | Hexadecimal | `2a` |
| `%o` | Octal | `52` |
| `%e` | Scientific notation | `3.140000e+00` |
| `%10s` | Right-align in 10 chars | |
| `%-10s` | Left-align in 10 chars | |
| `%05d` | Zero-pad to 5 digits | `00042` |

**Signature:** `static String format(String format, Object... args)`

---

## `formatted(Object... args)` *(Java 15+)*

Instance method equivalent of `String.format` — called directly on the format string.

```java
String result = "Hello, %s! You are %d years old.".formatted("Alice", 30);
System.out.println(result); // "Hello, Alice! You are 30 years old."
```

**Signature:** `String formatted(Object... args)`

---

## `StringJoiner` *(Java 8+)*

A utility class for building delimited strings with optional prefix and suffix — useful in loops.

```java
import java.util.StringJoiner;

StringJoiner sj = new StringJoiner(", ", "[", "]");
sj.add("Java");
sj.add("Python");
sj.add("Kotlin");
System.out.println(sj.toString()); // "[Java, Python, Kotlin]"

// With setEmptyValue for graceful empty handling
StringJoiner empty = new StringJoiner(", ", "[", "]");
empty.setEmptyValue("NONE");
System.out.println(empty.toString()); // "NONE"
```

---

# Section 8 — Type Conversion

## String → Primitive

```java
int    i  = Integer.parseInt("42");           // 42
long   l  = Long.parseLong("9876543210");     // 9876543210L
double d  = Double.parseDouble("3.14");       // 3.14
float  f  = Float.parseFloat("2.71");         // 2.71f
boolean b = Boolean.parseBoolean("true");     // true
char   c  = "A".charAt(0);                   // 'A'

// With radix (for non-decimal bases)
int hex = Integer.parseInt("FF", 16);         // 255
int bin = Integer.parseInt("1010", 2);        // 10
```

---

## Primitive → String

```java
String s1 = String.valueOf(42);        // "42"
String s2 = String.valueOf(3.14);      // "3.14"
String s3 = String.valueOf(true);      // "true"
String s4 = String.valueOf('A');       // "A"

// Integer/Long/Double wrapper toString
String s5 = Integer.toString(42);      // "42"
String s6 = Integer.toString(255, 16); // "ff" — hex representation
String s7 = Integer.toBinaryString(10); // "1010"
String s8 = Integer.toHexString(255);  // "ff"
String s9 = Integer.toOctalString(8);  // "10"

// Concatenation with empty string (works but not preferred)
String s10 = 42 + "";   // "42"
```

---

## String → char[]  and char[] → String

```java
// String to char array
char[] arr = "hello".toCharArray(); // ['h','e','l','l','o']

// char array to String
String s = new String(arr);          // "hello"
String s2 = String.valueOf(arr);     // "hello"

// char array range to String
String s3 = new String(arr, 1, 3);  // "ell" (offset=1, count=3)
```

---

# Section 9 — StringBuilder (Mutable Strings)

`StringBuilder` is the **mutable** counterpart to `String`. Use it whenever you need to build or modify a string across multiple steps — especially inside loops. It avoids the performance penalty of creating a new `String` object on every concatenation.

> **Thread Safety:** `StringBuilder` is **not thread-safe**. Use `StringBuffer` for multithreaded environments (same API, all methods synchronized).

## Creation

```java
StringBuilder sb1 = new StringBuilder();          // empty, capacity 16
StringBuilder sb2 = new StringBuilder(64);        // empty, capacity 64
StringBuilder sb3 = new StringBuilder("hello");   // "hello", capacity 21
```

---

## Core `StringBuilder` Operations

### `append()`

Appends a value to the end. Overloaded for all primitive types, `char[]`, `CharSequence`, and `Object`.

```java
StringBuilder sb = new StringBuilder();
sb.append("Hello");
sb.append(", ");
sb.append("World");
sb.append('!');
sb.append(42);
System.out.println(sb.toString()); // "Hello, World!42"

// Method chaining (append returns `this`)
String result = new StringBuilder()
    .append("Java")
    .append(" ")
    .append(21)
    .toString(); // "Java 21"
```

---

### `insert(int offset, ...)`

Inserts a value at the specified position.

```java
StringBuilder sb = new StringBuilder("Hello World");
sb.insert(5, ",");
System.out.println(sb); // "Hello, World"

sb.insert(0, ">>> ");
System.out.println(sb); // ">>> Hello, World"
```

---

### `delete(int start, int end)` / `deleteCharAt(int index)`

Removes a range of characters or a single character.

```java
StringBuilder sb = new StringBuilder("Hello World");
sb.delete(5, 11);           // Removes " World" → "Hello"
System.out.println(sb);     // "Hello"

sb.deleteCharAt(4);         // Removes 'o' → "Hell"
System.out.println(sb);     // "Hell"
```

---

### `replace(int start, int end, String str)`

Replaces a character range with a new string.

```java
StringBuilder sb = new StringBuilder("Hello World");
sb.replace(6, 11, "Java");
System.out.println(sb); // "Hello Java"
```

---

### `reverse()`

Reverses the character sequence in-place. Handles surrogate pairs correctly.

```java
StringBuilder sb = new StringBuilder("Java");
sb.reverse();
System.out.println(sb); // "avaJ"

// Classic interview: palindrome check
String s = "racecar";
boolean isPalindrome = s.equals(new StringBuilder(s).reverse().toString()); // true
```

---

### `charAt()` / `setCharAt()`

Read or modify a single character at an index.

```java
StringBuilder sb = new StringBuilder("hello");
System.out.println(sb.charAt(1));   // 'e'
sb.setCharAt(0, 'H');
System.out.println(sb);             // "Hello"
```

---

### `indexOf()` / `lastIndexOf()`

Same semantics as `String.indexOf()` and `String.lastIndexOf()`.

```java
StringBuilder sb = new StringBuilder("banana");
System.out.println(sb.indexOf("an"));     // 1
System.out.println(sb.lastIndexOf("an")); // 3
```

---

### `substring()`

Extracts a substring without modifying the builder.

```java
StringBuilder sb = new StringBuilder("Hello World");
System.out.println(sb.substring(6));     // "World"
System.out.println(sb.substring(0, 5));  // "Hello"
```

---

### `length()` / `capacity()` / `ensureCapacity()` / `trimToSize()`

```java
StringBuilder sb = new StringBuilder("Hello");
System.out.println(sb.length());    // 5 — current character count
System.out.println(sb.capacity());  // 21 — internal buffer capacity (5 + 16)

sb.ensureCapacity(100); // Ensure at least 100 character capacity
sb.trimToSize();        // Trim internal buffer to match current length
```

---

### `toString()`

Converts the `StringBuilder` back to an immutable `String`.

```java
StringBuilder sb = new StringBuilder("result");
String s = sb.toString(); // "result"
```

---

## StringBuilder vs String Concatenation Performance

```java
// BAD — O(N²) time: creates N intermediate String objects in the loop
String result = "";
for (int i = 0; i < 10000; i++) {
    result += i; // new String object every iteration
}

// GOOD — O(N) time: single mutable buffer, no intermediate objects
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 10000; i++) {
    sb.append(i);
}
String result = sb.toString();
```

---

# Section 10 — Text Blocks *(Java 15+)*

Text blocks allow multi-line string literals without manual newline escaping.

```java
String json = """
        {
            "name": "Alice",
            "age": 30
        }
        """;

String html = """
        <html>
            <body>
                <p>Hello, World!</p>
            </body>
        </html>
        """;

// Incidental whitespace is automatically stripped based on the
// position of the closing """ delimiter.
System.out.println(json);
```

---

# Section 11 — Regular Expressions with Strings

Java provides `java.util.regex.Pattern` and `java.util.regex.Matcher` for full regex capabilities beyond `String.matches()`.

## Pattern and Matcher

```java
import java.util.regex.*;

String text = "Phone: 123-456-7890 and 987-654-3210";
Pattern pattern = Pattern.compile("\\d{3}-\\d{3}-\\d{4}");
Matcher matcher = pattern.matcher(text);

// Find all matches
while (matcher.find()) {
    System.out.println("Found: " + matcher.group()); // 123-456-7890 then 987-654-3210
    System.out.println("Start: " + matcher.start() + ", End: " + matcher.end());
}
```

## Named Capture Groups

```java
Pattern p = Pattern.compile("(?<year>\\d{4})-(?<month>\\d{2})-(?<day>\\d{2})");
Matcher m = p.matcher("Today is 2024-01-15");

if (m.find()) {
    System.out.println(m.group("year"));  // "2024"
    System.out.println(m.group("month")); // "01"
    System.out.println(m.group("day"));   // "15"
}
```

## Common Regex Patterns Cheat Sheet

| Pattern | Matches |
|---|---|
| `\\d` | Any digit `[0-9]` |
| `\\D` | Any non-digit |
| `\\w` | Word character `[a-zA-Z0-9_]` |
| `\\W` | Non-word character |
| `\\s` | Whitespace (space, tab, newline) |
| `\\S` | Non-whitespace |
| `.` | Any character except newline |
| `^` | Start of string |
| `$` | End of string |
| `*` | Zero or more |
| `+` | One or more |
| `?` | Zero or one |
| `{n}` | Exactly n times |
| `{n,m}` | Between n and m times |
| `[abc]` | Any of a, b, c |
| `[^abc]` | None of a, b, c |
| `(a\|b)` | a or b |

---

# Section 12 — Common Interview Patterns

## Reverse a String

```java
// Method 1: StringBuilder (cleanest)
String reversed = new StringBuilder("hello").reverse().toString(); // "olleh"

// Method 2: Two-pointer on char array
char[] arr = "hello".toCharArray();
int left = 0, right = arr.length - 1;
while (left < right) {
    char temp = arr[left];
    arr[left++] = arr[right];
    arr[right--] = temp;
}
String reversed2 = new String(arr); // "olleh"
```

---

## Check Palindrome

```java
boolean isPalindrome(String s) {
    int left = 0, right = s.length() - 1;
    while (left < right) {
        if (s.charAt(left) != s.charAt(right)) return false;
        left++;
        right--;
    }
    return true;
}
```

---

## Frequency Map of Characters

```java
String s = "abracadabra";
int[] freq = new int[26]; // For lowercase English letters only
for (char c : s.toCharArray()) {
    freq[c - 'a']++;
}
// freq['a' - 'a'] = freq[0] = 5

// For full Unicode / mixed characters use HashMap
Map<Character, Integer> map = new HashMap<>();
for (char c : s.toCharArray()) {
    map.put(c, map.getOrDefault(c, 0) + 1);
}
```

---

## Anagram Check

```java
boolean isAnagram(String s, String t) {
    if (s.length() != t.length()) return false;
    int[] count = new int[26];
    for (char c : s.toCharArray()) count[c - 'a']++;
    for (char c : t.toCharArray()) count[c - 'a']--;
    for (int n : count) if (n != 0) return false;
    return true;
}
```

---

## Sort a String Alphabetically

```java
String s = "dcba";
char[] arr = s.toCharArray();
Arrays.sort(arr);
String sorted = new String(arr); // "abcd"
```

---

## Count Occurrences of a Character

```java
String s = "programming";
long count = s.chars().filter(c -> c == 'g').count(); // 2
// or
int count2 = s.length() - s.replace("g", "").length(); // 2
```

---

## Remove Duplicates (Preserve Order)

```java
String s = "programming";
StringBuilder sb = new StringBuilder();
Set<Character> seen = new LinkedHashSet<>();
for (char c : s.toCharArray()) {
    if (seen.add(c)) sb.append(c);
}
System.out.println(sb.toString()); // "progamin"
```

---

## String to Integer Without `parseInt`

```java
int myAtoi(String s) {
    s = s.trim();
    if (s.isEmpty()) return 0;
    int sign = 1, i = 0, result = 0;
    if (s.charAt(0) == '-') { sign = -1; i++; }
    else if (s.charAt(0) == '+') { i++; }
    while (i < s.length() && Character.isDigit(s.charAt(i))) {
        result = result * 10 + (s.charAt(i) - '0');
        i++;
    }
    return sign * result;
}
```

---

# Complexity Reference Table

| Operation | Time Complexity | Notes |
|---|---|---|
| `length()` | \(O(1)\) | Cached field lookup |
| `charAt(i)` | \(O(1)\) | Direct array index |
| `indexOf(char)` | \(O(N)\) | Linear scan |
| `indexOf(String)` | \(O(N \cdot M)\) | N = text length, M = pattern length |
| `contains(str)` | \(O(N \cdot M)\) | Delegates to `indexOf` |
| `startsWith` / `endsWith` | \(O(M)\) | M = prefix/suffix length |
| `substring(i, j)` | \(O(j - i)\) | Copies characters |
| `equals(str)` | \(O(N)\) | Character-by-character comparison |
| `compareTo(str)` | \(O(N)\) | Stops at first difference |
| `toLowerCase` / `toUpperCase` | \(O(N)\) | New string created |
| `trim` / `strip` | \(O(N)\) | New string created |
| `replace(char, char)` | \(O(N)\) | Single pass |
| `replace(String, String)` | \(O(N \cdot M)\) | Pattern scan |
| `replaceAll(regex)` | \(O(N)\) amortized | Regex compilation overhead |
| `split(regex)` | \(O(N)\) | Single scan |
| `toCharArray()` | \(O(N)\) | Full copy |
| `String.join(...)` | \(O(N)\) | Linear in total output length |
| `String.format(...)` | \(O(N)\) | Linear in output length |
| `StringBuilder.append()` | \(O(1)\) amortized | Occasional buffer resize |
| `StringBuilder.reverse()` | \(O(N)\) | In-place swap |
| `StringBuilder.insert()` | \(O(N)\) | Shift characters right |
| `StringBuilder.delete()` | \(O(N)\) | Shift characters left |
| `StringBuilder.toString()` | \(O(N)\) | Copies buffer to new String |
| `intern()` | \(O(N)\) | Pool lookup + copy |

---

# Key Takeaways

- **Strings are immutable.** Every transformation method returns a new `String` object — always capture the return value. Ignoring the return value achieves nothing.
- **Use `StringBuilder` in loops.** String `+` concatenation inside a loop creates \(O(N^2)\) intermediate objects and is one of the most common Java performance anti-patterns. A `StringBuilder` reduces this to \(O(N)\) amortized time.
- **Use `.equals()`, never `==`.** The `==` operator compares object references, not character content. It produces correct results only by coincidence for string pool literals — and silently fails for dynamically created strings.
- **Prefer `strip()` over `trim()` in Java 11+.** `trim()` only removes ASCII whitespace (≤ `\u0020`). `strip()` handles all Unicode whitespace categories correctly.
- **Seed `String.format` knowledge deeply.** Format specifiers (`%s`, `%d`, `%.2f`, `%-10s`, `%05d`) appear constantly in output formatting, logging, test assertions, and competitive programming.
- **`split("\\s+")` is the idiomatic word tokenizer.** It collapses any run of whitespace (spaces, tabs, newlines) into a single split boundary — far more robust than `split(" ")` for real-world input.
- **`toCharArray()` is the gateway to character-level algorithm problems.** Frequency maps, palindrome checks, anagram detection, sliding window character counts — they all begin with `s.toCharArray()`.
- Core concepts exercised by string operations:
  - Immutability and the String Pool
  - Index arithmetic and boundary conditions
  - Character encoding (ASCII, Unicode, UTF-8)
  - Regular expressions
  - Amortized analysis (StringBuilder buffer growth)
