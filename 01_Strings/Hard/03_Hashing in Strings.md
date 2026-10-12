# String Hashing (Polynomial Rolling Hash)

## Problem Statement

**String Hashing** is a technique that maps a string of characters to a fixed-size integer value (a hash code). This mapping allows us to compare two strings for equality in **O(1) constant time** instead of running a traditional character-by-character linear scan.

The primary goal of an optimal string hashing algorithm is to generate unique, well-distributed hash values with an extremely low probability of hash collisions, while supporting dynamic **rolling calculations** for substring intervals.

A standard rolling hash implementation should support the following core operations efficiently:

```text
1. computeHash(String s)
2. getSubstringHash(int left, int right)
```

The overall run time complexity for substring hash retrieval should be:

```text
O(1)
```

---

## Key Idea: Polynomial Rolling Hash

The most widely used and reliable string hashing mechanism is the **Polynomial Rolling Hash Function**. 

A string S of length L is viewed as a polynomial where each character acts as a coefficient for an escalating base value p:

\[Hash(S) = (S[0] \cdot p^{L-1} + S[1] \cdot p^{L-2} + \dots + S[L-1] \cdot p^0) \pmod m\]

### Constants Selection
To distribute hashes evenly and avoid structural collisions, the constants p and m must be carefully chosen:
- **Base (p):** A prime number slightly larger than the total size of the active character alphabet. For instance, p = 31 works well for lowercase English letters, and p = 53 handles both uppercase and lowercase letters.
- **Modulus (m):** A large prime number to restrict the hash values within an integer data capacity while maximizing the hash spectrum spread. Commonly used primes include m = 10⁹ + 7 or m = 10⁹ + 9.

---

## Prefix Hash & Substring Extraction

By precalculating the **prefix hash values** of a string, we can extract the exact hash of *any* substring \(S[i \dots j]\) in constant O(1) time. 

Let \(Prefix[k]\) be the hash of the prefix string up to index k-1. The hash of the substring starting at index i and ending at index j is calculated mathematically as:

\[Hash(S[i \dots j]) = (Prefix[j + 1] - Prefix[i] \cdot p^{j - i + 1}) \pmod m\]

We multiply the earlier prefix total by \(p^{j - i + 1}\) to shift its polynomial powers to align perfectly with the tracking baseline of the later segment.

---

## Visual Understanding

Suppose we hash the string `"abc"` using p = 31 and m = 10⁹ + 7:
- Let individual character integer coefficients be mapped as 'a'=1, 'b'=2, 'c'=3.

The rolling prefix array states expand step-by-step:
```text
Prefix[0] = 0
Prefix[1] = (0 * 31 + 1) = 1
Prefix[2] = (1 * 31 + 2) = 33
Prefix[3] = (33 * 31 + 3) = 1023 + 3 = 1026
```

If we want to extract the substring hash for `"bc"` (indices `1` to `2`):
- \(Prefix[3] = 1026\)
- \(Prefix[1] = 1\)
- Shift Factor: \(p^{2 - 1 + 1} = p^2 = 31^2 = 961\)

\[Hash(\text{"bc"}) = (1026 - 1 \cdot 961) = 1026 - 961 = 65\]

Let's verify computing `"bc"` directly: (2 ⋅ 31¹ + 3 ⋅ 31⁰) = 62 + 3 = 65. Both values match perfectly in O(1) time.

---

## Java Solution

```java
class StringHash {

    private final long[] prefixHash;
    private final long[] powerCache;
    private final long p = 31; // Prime base for lowercase English letters
    private final long m = 1000000007; // Large prime modulus

    public StringHash(String s) {
        int n = s.length();
        this.prefixHash = new long[n + 1];
        this.powerCache = new long[n + 1];

        powerCache[0] = 1;
        // Step 1: Precalculate polynomial base powers and prefix hashes
        for (int i = 0; i < n; i++) {
            powerCache[i + 1] = (powerCache[i] * p) % m;
            
            // Map character to an integer value ('a' -> 1, 'b' -> 2, etc.)
            long charVal = s.charAt(i) - 'a' + 1;
            prefixHash[i + 1] = (prefixHash[i] * p + charVal) % m;
        }
    }

    // Step 2: Extract any substring hash interval in O(1) time
    public int getSubstringHash(int left, int right) {
        long rawHash = prefixHash[right + 1] - (prefixHash[left] * powerCache[right - left + 1]) % m;
        
        // Handle negative modulo values in Java gracefully
        if (rawHash < 0) {
            rawHash += m;
        }
        
        return (int) rawHash;
    }
}
```

---

## Dry Run

### Operations
```java
StringHash hasher = new StringHash("abcab");
int hash1 = hasher.getSubstringHash(0, 1); // Hash for "ab"
int hash2 = hasher.getSubstringHash(3, 4); // Hash for "ab"
boolean isEqual = (hash1 == hash2); // true
```

---

### Step Execution Traversal

1. **Initialization:** Processes the input `"abcab"`. The arrays `prefixHash` and `powerCache` are pre-populated completely across the length of the string in a single linear sweep.
2. **`getSubstringHash(0, 1)`**: Matches indices for the first `"ab"`. It computes the relative power shift difference and extracts the baseline modulo code value.
3. **`getSubstringHash(3, 4)`**: Matches indices for the second trailing `"ab"`. Because the characters are identical and follow the exact same sequential arrangement rules, the polynomial equation results in the exact same numeric integer hash value.
4. **Comparison Step:** `hash1 == hash2` evaluates to `true` instantly without reading or validating character indices directly anymore.

---

## Why Use a Rolling Hash over Standard String Slicing?

For dense matching problems (e.g., searching for a sub-pattern across a large text body), standard string operations impose high processing bottlenecks:
- **Standard Slicing:** Comparing strings or generating substrings copies characters, resulting in a costly O(L) runtime penalty per check.
- **Rolling Calculation Balance:** String Hashing reduces string comparisons into a simple integer verification step. Pre-calculating prefix states allows any arbitrary substring hash to be extracted in true O(1) constant time, bypassing character matching limits completely.

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Reason |
| :--- | :--- | :--- |
| **`Initialization`** | **O(n)** | Traverses and sets up prefix hashes and power arrays exactly once. |
| **`getSubstringHash`** | **O(1)** | Evaluates a simple fixed mathematical equation with no loops. |

---

### Space Complexity

```text
O(n)
```

The data structure allocates two parallel primitive tracking arrays of size `n + 1` to hold precalculated hash sums and modular exponent values.

---

## Key Insight

Mapping variable character patterns into stable polynomial numerical sums allows long text strings to be evaluated and compared through constant-time mathematical equations.

```text
Time  : O(1) per substring hash query
Space : O(n) array storage memory allocation
```

---

## Similar Problems

1. Find the Index of the First Occurrence in a String / Rabin-Karp (28)
2. Longest Duplicate Substring (1044)
3. Repeated String Match (686)
4. Distinct Echo Substrings (1316)
5. Shortest Palindrome (214)
