# Java Type Conversion and Character Operations

## Code

```java
class Main {
    public static void main(String[] args) {

        int a = 100;

        char ch = (char) a;

        char num = '2';

        System.out.println(ch);                     // d
        System.out.println((int) ch);              // 100
        System.out.println(Integer.valueOf(ch));   // 100

        System.out.println(Integer.parseInt("123")); // 123

        System.out.println(String.valueOf(a));     // "100"
        System.out.println(String.valueOf(ch));    // "d"

        System.out.println(ch - 'a');              // 3

        System.out.println(ch + 1);                // 101

        System.out.println((char) (ch + 1));       // e

        System.out.println(ch + num);              // 150

        System.out.println(Character.getNumericValue('7')); // 7

        System.out.println(Character.getNumericValue('A')); // 10
    }
}
```

---

# Output

```text
d
100
100
123
100
d
3
101
e
150
7
10
```

---

# Understanding Character and ASCII Conversion

## Integer to Character

```java
int a = 100;

char ch = (char) a;
```

ASCII/Unicode value:

```text
100 → 'd'
```

Therefore:

```java
System.out.println(ch);
```

Output:

```text
d
```

---

## Character to Integer

```java
System.out.println((int) ch);
```

The character:

```java
'd'
```

has ASCII value:

```text
100
```

Output:

```text
100
```

---

## Using Integer.valueOf()

```java
System.out.println(Integer.valueOf(ch));
```

Since:

```java
ch = 'd'
```

Java converts the character to its integer value.

Output:

```text
100
```

---

# String to Integer

```java
System.out.println(
    Integer.parseInt("123")
);
```

Converts:

```text
"123" → 123
```

Output:

```text
123
```

---

# Integer to String

```java
System.out.println(
    String.valueOf(a)
);
```

Converts:

```text
100 → "100"
```

Output:

```text
100
```

---

# Character to String

```java
System.out.println(
    String.valueOf(ch)
);
```

Converts:

```text
'd' → "d"
```

Output:

```text
d
```

---

# Finding Alphabet Position

```java
System.out.println(ch - 'a');
```

Values:

```text
'd' = 100
'a' = 97
```

Calculation:

```text
100 - 97 = 3
```

Output:

```text
3
```

---

## Common Trick

Find alphabet index:

```java
char c = 'd';

int index = c - 'a';
```

Result:

```text
3
```

For 1-based position:

```java
(c - 'a') + 1
```

Result:

```text
4
```

---

# Character + Integer

```java
System.out.println(ch + 1);
```

Values:

```text
'd' = 100
```

Calculation:

```text
100 + 1 = 101
```

Output:

```text
101
```

---

# Next Character

```java
System.out.println(
    (char)(ch + 1)
);
```

Calculation:

```text
'd' → 100
100 + 1 = 101
101 → 'e'
```

Output:

```text
e
```

---

# Character + Character

```java
System.out.println(ch + num);
```

Values:

```text
'd' = 100
'2' = 50
```

Calculation:

```text
100 + 50
=
150
```

Output:

```text
150
```

---

## Important Interview Point

```java
'2'
```

is a character, not the number:

```text
Character '2'
=
ASCII 50
```

while

```java
2
```

is the integer:

```text
2
```

---

# Character.getNumericValue()

Used to get the numerical meaning of a character.

---

## Digit Character

```java
Character.getNumericValue('7')
```

Output:

```text
7
```

---

## Alphabet Character

```java
Character.getNumericValue('A')
```

Output:

```text
10
```

Reason:

```text
A → 10
B → 11
C → 12
...
Z → 35
```

Similar to hexadecimal/alphanumeric numbering.

---

# ASCII Values Cheat Sheet

## Digits

| Character | ASCII |
|------------|--------|
| '0' | 48 |
| '1' | 49 |
| '2' | 50 |
| '3' | 51 |
| '4' | 52 |
| '5' | 53 |
| '6' | 54 |
| '7' | 55 |
| '8' | 56 |
| '9' | 57 |

---

## Uppercase Letters

| Character | ASCII |
|------------|--------|
| 'A' | 65 |
| 'B' | 66 |
| 'C' | 67 |
| ... | ... |
| 'Z' | 90 |

---

## Lowercase Letters

| Character | ASCII |
|------------|--------|
| 'a' | 97 |
| 'b' | 98 |
| 'c' | 99 |
| 'd' | 100 |
| ... | ... |
| 'z' | 122 |

---

# Common Interview Tricks

## Convert Digit Character to Integer

### Method 1

```java
char c = '7';

int num = c - '0';
```

Output:

```text
7
```

---

### Method 2

```java
Character.getNumericValue(c)
```

Output:

```text
7
```

---

## Convert Integer to Character Digit

```java
int num = 7;

char c = (char)(num + '0');
```

Output:

```text
'7'
```

---

## Convert Lowercase to Uppercase

```java
char ch = 'd';

char upper =
    (char)(ch - 32);
```

Output:

```text
'D'
```

---

## Convert Uppercase to Lowercase

```java
char ch = 'D';

char lower =
    (char)(ch + 32);
```

Output:

```text
'd'
```

---

# Time Complexity

All operations shown above are:

```text
O(1)
```

because they involve direct type conversion and arithmetic operations.

---

# Key Takeaways

```java
(char)100
```

```text
→ 'd'
```

---

```java
(int)'d'
```

```text
→ 100
```

---

```java
'd' - 'a'
```

```text
→ 3
```

---

```java
(char)('d' + 1)
```

```text
→ 'e'
```

---

```java
'2'
```

```text
→ ASCII 50
```

not integer 2.

---

```java
Character.getNumericValue('7')
```

```text
→ 7
```

---

```java
Character.getNumericValue('A')
```

```text
→ 10
```

# Additional Useful Character & Type Conversion Operations in Java

## Character Classification Methods

### Check if Character is a Digit

```java
System.out.println(Character.isDigit('5'));
```

Output:

```text
true
```

---

### Check if Character is an Alphabet

```java
System.out.println(Character.isLetter('A'));
```

Output:

```text
true
```

---

### Check if Character is Lowercase

```java
System.out.println(Character.isLowerCase('d'));
```

Output:

```text
true
```

---

### Check if Character is Uppercase

```java
System.out.println(Character.isUpperCase('D'));
```

Output:

```text
true
```

---

### Check if Character is Letter or Digit

```java
System.out.println(Character.isLetterOrDigit('7'));
System.out.println(Character.isLetterOrDigit('A'));
System.out.println(Character.isLetterOrDigit('$'));
```

Output:

```text
true
true
false
```

---

### Check if Character is Whitespace

```java
System.out.println(Character.isWhitespace(' '));
System.out.println(Character.isWhitespace('n'));
```

Output:

```text
true
true
```

---

# Case Conversion

### Convert to Uppercase

```java
System.out.println(
    Character.toUpperCase('d')
);
```

Output:

```text
D
```

---

### Convert to Lowercase

```java
System.out.println(
    Character.toLowerCase('D')
);
```

Output:

```text
d
```

---

### Toggle Case

```java
char ch = 'D';

if (Character.isUpperCase(ch))
    ch = Character.toLowerCase(ch);
else
    ch = Character.toUpperCase(ch);

System.out.println(ch);
```

Output:

```text
d
```

---

# Character Arithmetic

### Difference Between Two Characters

```java
System.out.println('z' - 'a');
```

Output:

```text
25
```

---

### Number of Letters Between Characters

```java
System.out.println('h' - 'a');
```

Output:

```text
7
```

---

### Get Character From Index

```java
int index = 3;

char ch = (char)('a' + index);

System.out.println(ch);
```

Output:

```text
d
```

---

### Generate Alphabet

```java
for(char ch = 'a'; ch <= 'z'; ch++) {
    System.out.print(ch + " ");
}
```

Output:

```text
a b c d e ... z
```

---

# Character to Integer Conversion

### Using ASCII

```java
char digit = '7';

int num = digit - '0';

System.out.println(num);
```

Output:

```text
7
```

---

### Using Character.getNumericValue()

```java
System.out.println(
    Character.getNumericValue('7')
);
```

Output:

```text
7
```

---

### Difference

```java
Character.getNumericValue('A')
```

Output:

```text
10
```

while

```java
'A' - '0'
```

Output:

```text
17
```

because:

```text
65 - 48 = 17
```

---

# Integer to Character Conversion

### Convert Integer Digit to Character

```java
int num = 7;

char ch = (char)(num + '0');

System.out.println(ch);
```

Output:

```text
7
```

---

### Convert Number to Alphabet

```java
int index = 0;

char ch = (char)('a' + index);

System.out.println(ch);
```

Output:

```text
a
```

---

# String and Character Conversion

### Character to String

```java
char ch = 'd';

String str =
        String.valueOf(ch);

System.out.println(str);
```

Output:

```text
d
```

---

### String to Character

```java
String str = "hello";

char ch = str.charAt(0);

System.out.println(ch);
```

Output:

```text
h
```

---

### Character Array to String

```java
char[] arr =
        {'h','e','l','l','o'};

String str =
        new String(arr);

System.out.println(str);
```

Output:

```text
hello
```

---

### String to Character Array

```java
String str = "hello";

char[] arr =
        str.toCharArray();
```

Output:

```text
[h,e,l,l,o]
```

---

# ASCII Tricks

### ASCII of Character

```java
System.out.println((int)'A');
```

Output:

```text
65
```

---

### Character From ASCII

```java
System.out.println((char)65);
```

Output:

```text
A
```

---

### Lowercase to Uppercase

```java
char lower = 'd';

char upper =
        (char)(lower - 32);

System.out.println(upper);
```

Output:

```text
D
```

---

### Uppercase to Lowercase

```java
char upper = 'D';

char lower =
        (char)(upper + 32);

System.out.println(lower);
```

Output:

```text
d
```

---

# Binary and Numeric Conversions

### String to Integer

```java
int n =
    Integer.parseInt("123");
```

Output:

```text
123
```

---

### Integer to String

```java
String str =
    String.valueOf(123);
```

Output:

```text
"123"
```

---

### Integer to Binary String

```java
System.out.println(
    Integer.toBinaryString(10)
);
```

Output:

```text
1010
```

---

### Binary String to Integer

```java
System.out.println(
    Integer.parseInt("1010", 2)
);
```

Output:

```text
10
```

A **radix** (also called the base) is the number of unique digits used to represent numbers in a positional numeral system. It dictates the value of each digit position and tells the system when to "roll over" to the next place value.

### Common Radix Systems

| System | Radix (Base) | Allowed Digits | Example |
| :--- | :--- | :--- | :--- |
| **Binary** | 2 | 0, 1 | 1010 (Decimal 10) |
| **Octal** | 8 | 0, 1, 2, 3, 4, 5, 6, 7 | 12 (Decimal 10) |
| **Decimal** | 10 | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 | 10 (Our everyday system) |
| **Hexadecimal** | 16 | 0-9 and A-F (where A=10, F=15) | A (Decimal 10) |


### Radix Parsing Examples in Java

| Java Statement | Radix (Base) | Calculation | Result (Decimal) |
| :--- | :--- | :--- | :--- |
| `Integer.parseInt("1010", 2)` | Binary (2) | (1\*2^3 + 0\*2^2 + 1*2^1 + 0*2^0) | 10 |
| `Integer.parseInt("1010", 8)` | Octal (8) | (1*8^3 + 0*8^2 + 1*8^1 + 0*8^0) | 520 |
| `Integer.parseInt("1010", 10)` | Decimal (10) | (1*10^3 + 0*10^2 + 1*10^1 + 0*10^0) | 1010 |
| `Integer.parseInt("1010", 16)` | Hexadecimal (16) | (1*16^3 + 0*16^2 + 1*16^1 + 0*16^0) | 4112 |

---

# Frequently Used Interview Tricks

### Check if Vowel

```java
char ch = 'e';

boolean vowel =
    "aeiouAEIOU".indexOf(ch) != -1;

System.out.println(vowel);
```

Output:

```text
true
```

---

### Character Frequency Array Index

```java
char ch = 'd';

int index = ch - 'a';

System.out.println(index);
```

Output:

```text
3
```

Used extensively in:

```text
Hashing
Trie
Sliding Window
Strings
```

---

### Digit Frequency Array Index

```java
char digit = '7';

int index = digit - '0';

System.out.println(index);
```

Output:

```text
7
```

---

### Check Anagram Using Frequency Array

```java
freq[ch - 'a']++;
```

Extremely common FAANG pattern.

---

# Character Utility Methods Cheat Sheet

| Method | Example | Output |
|----------|----------|----------|
| isDigit() | Character.isDigit('5') | true |
| isLetter() | Character.isLetter('A') | true |
| isUpperCase() | Character.isUpperCase('A') | true |
| isLowerCase() | Character.isLowerCase('a') | true |
| isWhitespace() | Character.isWhitespace(' ') | true |
| toUpperCase() | Character.toUpperCase('a') | 'A' |
| toLowerCase() | Character.toLowerCase('A') | 'a' |
| getNumericValue() | Character.getNumericValue('7') | 7 |
| compare() | Character.compare('a','b') | -1 |

---

# Most Important Character Tricks for DSA

```java
ch - 'a'        // alphabet index

ch - 'A'        // uppercase index

digit - '0'     // char digit → int

(char)(num+'0') // int → char digit

Character.isDigit(ch)

Character.isLetter(ch)

Character.toLowerCase(ch)

Character.toUpperCase(ch)

str.toCharArray()

String.valueOf(ch)

Character.getNumericValue(ch)
```

These operations appear repeatedly in:

```text
✓ String Problems
✓ Hashing
✓ Trie
✓ Sliding Window
✓ Backtracking
✓ Recursion
✓ Bit Manipulation
✓ Competitive Programming
✓ FAANG Interviews
```




