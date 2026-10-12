# Valid IP Address

## Problem Statement

Given a string `queryIP`, return `"IPv4"` if the IP address is a valid IPv4 address, `"IPv6"` if the IP address is a valid IPv6 address, or `"Neither"` if the IP address is not valid.

**IPv4 addresses** must follow a four-part dotted-decimal format (`"x1.x2.x3.x4"`):
* Each \(x_i\) is a **decimal integer** between `0` and `255`.
* **No leading zeros** are allowed for any part (e.g., `"172.16.254.01"` is invalid).

**IPv6 addresses** must follow an eight-part colon-hexadecimal format (`"x1:x2:x3:x4:x5:x6:x7:x8"`):
* Each \(x_i\) is a **hexadecimal string** containing between `1` and `4` characters.
* Valid characters include digits (`'0'-'9'`), lowercase letters (`'a'-'f'`), and uppercase letters (`'A'-'F'`).
* Leading zeros *are* allowed in individual parts (e.g., `"2001:0db8:85a3:0000:0000:8a2e:0370:7334"` is valid).

The overall runtime complexity must be:

```text
O(n)
```

*(where n is the length of the string queryIP)*

---

## Examples

### Example 1

**Input**

```java
queryIP = "172.16.254.1"
```

**Output**

```java
"IPv4"
```

---

### Example 2

**Input**

```java
queryIP = "2001:0db8:85a3:0:0:8A2E:0370:7334"
```

**Output**

```java
"IPv6"
```

---

### Example 3

**Input**

```java
queryIP = "256.256.256.256"
```

**Output**

```java
"Neither"
```

---

## Brute Force Approach

Use regular expressions (`Pattern.matches()`) to validate the entire structure of the string in one go.

### Steps

1. Construct a heavy regex string for IPv4 checking rules, ensuring numbers are capped up to 255 and leading zeros are blocked.
2. Construct another regex string for IPv6 checking rules, validating hexadecimal character limits per token block.
3. Invoke pattern evaluations and return the classified string token.

### Complexity

```text
Time Complexity: O(n) // but carries heavy backtracking performance penalties
Space Complexity: O(n) for matching tables
```

While regular expressions provide a compact look, they often mask inner token-splitting penalties and complicate edge case tracking. We can optimize this process down to a fast **token validation scan** by splitting text components safely.

---

# Optimal Approach: Tokenized Validation Scan

## Key Idea

Instead of matching the entire string, we can split the text dynamically using delimiter anchor tokens (`.` or `:`) and validate each isolated block according to its protocol specifications.

1. **Format Routing:** If `queryIP` contains more than one `'.'`, evaluate it under IPv4 guidelines. If it contains more than one `':'`, evaluate it under IPv6 guidelines. Otherwise, return `"Neither"`.
2. **Boundary Deficit Correction:** To prevent Java's `.split()` function from discarding trailing empty parts (e.g., `"1.1.1.1."` splitting into only 4 tokens instead of a flagged 5), append a dummy placeholder suffix to the string or pass an explicit negative limit argument: `queryIP.split("\\.", -1)`.
3. **IPv4 Validation Pass:**
   - The token array must contain exactly **4 components**.
   - Each component must have a length between **1 and 3 characters**.
   - Characters must be digits only. If length > 1, the first character **cannot be `'0'`**.
   - The parsed integer value must reside securely between **0 and 255**.
4. **IPv6 Validation Pass:**
   - The token array must contain exactly **8 components**.
   - Each component must have a length between **1 and 4 characters**.
   - Every character must belong strictly to the hexadecimal character sets (`'0'-'9'`, `'a'-'f'`, `'A'-'F'`).

---

## Visual Understanding

Suppose `queryIP = "172.16.254.01"`:

- **Routing:** Contains `'.'`, split components with limit `-1` \(\implies\) `["172", "16", "254", "01"]`. Length is 4.
- **Token 0 ("172"):** Valid digits. Value 172 is between 0 and 255. Pass.
- **Token 1 ("16"):** Valid digits. Value 16 is between 0 and 255. Pass.
- **Token 2 ("254"):** Valid digits. Value 254 is between 0 and 255. Pass.
- **Token 3 ("01"):** Length is 2. The first character is `'0'`. **Leading zero contract violated!** The validation routine returns `"Neither"` instantly.

---

## Partition Variables

Let:

```java
String[] tokensv4 = queryIP.split("\\.", -1);
String[] tokensv6 = queryIP.split(":", -1);
```

---

### Border Elements

Passing the explicit negative limit `-1` to the split method ensures trailing empty slices are tracked, capturing invalid input layouts like `"1.1.1.1."` or `"2001:db8:85a3:0:0:8a2e:0370:7334:"` automatically:

```java
String[] tokens = queryIP.split("\\.", -1);
if (tokens.length != 4) return "Neither";
```

---

## Correct Partition Condition

The hexadecimal check filters out alphanumeric noise using a direct boundary range assertion:

```java
for (char c : token.toCharArray()) {
    boolean isDigit = c >= '0' && c <= '9';
    boolean isLowHex = c >= 'a' && c <= 'f';
    boolean isUpHex = c >= 'A' && c <= 'F';
    if (!isDigit && !isLowHex && !isUpHex) return false;
}
```

---

## Java Solution

```java
class Solution {

    public String validIPAddress(String queryIP) {
        if (queryIP == null || queryIP.isEmpty()) {
            return "Neither";
        }

        // Route queries based on token anchor patterns
        if (queryIP.chars().filter(ch -> ch == '.').count() == 3) {
            return isValidIPv4(queryIP) ? "IPv4" : "Neither";
        } else if (queryIP.chars().filter(ch -> ch == ':').count() == 7) {
            return isValidIPv6(queryIP) ? "IPv6" : "Neither";
        }

        return "Neither";
    }

    private boolean isValidIPv4(String ip) {
        // Passing -1 captures empty trailing tokens securely (e.g., "1.1.1.1.")
        String[] tokens = ip.split("\\.", -1);
        if (tokens.length != 4) return false;

        for (String token : tokens) {
            // Part length constraint verification
            if (token.length() == 0 || token.length() > 3) return false;
            
            // Leading zero constraint block
            if (token.length() > 1 && token.charAt(0) == '0') return false;

            int value = 0;
            for (int i = 0; i < token.length(); i++) {
                char c = token.charAt(i);
                if (c < '0' || c > '9') return false; // Non-digit symbol filtered
                value = value * 10 + (c - '0');
            }

            // Numeric range confirmation
            if (value < 0 || value > 255) return false;
        }
        return true;
    }

    private boolean isValidIPv6(String ip) {
        String[] tokens = ip.split(":", -1);
        if (tokens.length != 8) return false;

        for (String token : tokens) {
            // Hex segment length constraint verification
            if (token.length() == 0 || token.length() > 4) return false;

            // Character validation check across hexadecimal rulesets
            for (int i = 0; i < token.length(); i++) {
                char c = token.charAt(i);
                boolean isDigit = c >= '0' && c <= '9';
                boolean isLowHex = c >= 'a' && c <= 'f';
                boolean isUpHex = c >= 'A' && c <= 'F';
                
                if (!isDigit && !isLowHex && !isUpHex) {
                    return false;
                }
            }
        }
        return true;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Tokenized Scan** | **O(n)** | **O(n)** | Traverses the characters of the string a fixed number of times during tokenization splits. The token arrays allocate temporary memory spaces matching the input sizes. |

---

## Key Insight

Forcing the text split routines to preserve empty trailing slots with a negative array cap prevents subtle parsing escapes, classifying network protocols inside safe linear bounds.

```text
Time  : O(n) single pass parsing pass
Space : O(n) temporary token split allocations
```

---

## Similar Problems

1. String to Integer / atoi (8)
2. Simplify Path (71)
3. Defanging an IP Address (1108)
4. Complex Number Multiplication (537)
5. Parse Lisp Expression (736)
