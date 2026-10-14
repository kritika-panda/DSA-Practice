# Design Authentication Manager (LeetCode 1797)

## Problem Statement

You need to implement an authentication manager with a token expiration mechanism.

Each token has:

```text
tokenId
expirationTime
```

The authentication manager supports the following operations:

### AuthenticationManager(int timeToLive)

Construct the authentication manager and set the token lifetime.

---

### generate(String tokenId, int currentTime)

Generates a new token that expires at:

```text
currentTime + timeToLive
```

---

### renew(String tokenId, int currentTime)

Renews an unexpired token.

If:

```text
expirationTime <= currentTime
```

the token has already expired and cannot be renewed.

---

### countUnexpiredTokens(int currentTime)

Returns the number of unexpired tokens.

A token is unexpired if:

```text
expirationTime > currentTime
```

---

## Example

### Input

```text
["AuthenticationManager",
 "generate",
 "renew",
 "generate",
 "countUnexpiredTokens",
 "generate",
 "renew",
 "countUnexpiredTokens"]

[[5],
 ["aaa",2],
 ["aaa",4],
 ["bbb",6],
 [6],
 ["ccc",7],
 ["aaa",8],
 [15]]
```

### Output

```text
[null,null,null,null,1,null,null,0]
```

---

# Key Observation

For every token we only care about:

```text
tokenId → expirationTime
```

Operations required:

### Generate

```text
Insert token
```

### Renew

```text
Update expiration time
```

### Count

```text
Count all tokens with

expirationTime > currentTime
```

---

# Data Structure

Use:

```java
HashMap<String, Integer>
```

where:

```text
Key   = tokenId
Value = expirationTime
```

Example:

```text
{
   aaa -> 9
   bbb -> 11
}
```

---

# Generate Operation

```java
generate("aaa", 2)
```

TTL:

```text
5
```

Store:

```text
aaa -> 7
```

because:

```text
2 + 5 = 7
```

---

# Renew Operation

Renew only when token exists and is not expired.

Condition:

```java
expiry > currentTime
```

Example:

```text
aaa -> 7

renew("aaa", 4)
```

Valid because:

```text
7 > 4
```

New expiry:

```text
4 + 5 = 9
```

Store:

```text
aaa -> 9
```

---

# Count Operation

Traverse all expirations.

Count:

```text
expiry > currentTime
```

---

# Dry Run

## TTL = 5

---

### generate("aaa",2)

```text
aaa -> 7
```

---

### renew("aaa",4)

```text
7 > 4
```

Renew:

```text
aaa -> 9
```

---

### generate("bbb",6)

```text
bbb -> 11
```

Map:

```text
aaa -> 9
bbb -> 11
```

---

### countUnexpiredTokens(6)

```text
aaa -> 9 > 6
bbb -> 11 > 6
```

Count:

```text
2
```

---

### renew("aaa",10)

```text
aaa expiry = 9

9 <= 10
```

Already expired.

Cannot renew.

---

### countUnexpiredTokens(15)

```text
aaa -> expired
bbb -> expired
```

Result:

```text
0
```

---

# Java Solution

```java
import java.util.*;

class AuthenticationManager {

    private int timeToLive;
    private Map<String, Integer> map;

    public AuthenticationManager(int timeToLive) {
        this.timeToLive = timeToLive;
        this.map = new HashMap<>();
    }

    public void generate(String tokenId, int currentTime) {
        map.put(tokenId, currentTime + timeToLive);
    }

    public void renew(String tokenId, int currentTime) {

        if (!map.containsKey(tokenId)) {
            return;
        }

        int expiry = map.get(tokenId);

        if (expiry > currentTime) {
            map.put(tokenId, currentTime + timeToLive);
        }
    }

    public int countUnexpiredTokens(int currentTime) {

        int count = 0;

        for (int expiry : map.values()) {

            if (expiry > currentTime) {
                count++;
            }
        }

        return count;
    }
}
```

---

# Why Does This Work?

The HashMap always stores:

```text
Latest expiration time
```

for every token.

### Generate

```text
Insert token
```

### Renew

```text
Update expiration only if token
is still valid.
```

### Count

```text
Scan all tokens and count valid ones.
```

This directly follows the problem definition.

---

# Correctness Proof

For every token:

```text
map[tokenId] = expirationTime
```

is maintained correctly.

### Generate

Stores:

```text
currentTime + TTL
```

which is the correct expiration time.

---

### Renew

Updates expiration only when:

```text
expirationTime > currentTime
```

which means the token is still valid.

Expired tokens remain unchanged.

---

### Count

Counts exactly those tokens whose:

```text
expirationTime > currentTime
```

Therefore the returned count is correct.

---

# Complexity Analysis

Let:

```text
N = number of stored tokens
```

### Generate

```text
O(1)
```

---

### Renew

```text
O(1)
```

---

### Count

Traverse all tokens:

```text
O(N)
```

---

### Space Complexity

```text
O(N)
```

for storing the tokens.

---

# Follow-Up: Can We Optimize countUnexpiredTokens()?

The LeetCode constraints allow the above solution.

However, for large systems:

```text
countUnexpiredTokens() = O(N)
```

may be expensive.

A common optimization uses:

```text
HashMap + Queue
```

or

```text
HashMap + Min Heap
```

to lazily remove expired tokens.

This can make counting much faster by cleaning expired entries only when needed.

---

# Pattern Recognition

This problem is recognized by:

```text
✓ Token management
✓ Expiration timestamps
✓ Generate / Renew operations
✓ Key-value lookup
✓ Design Data Structure
```

---

# Similar Problems

1. LRU Cache
2. LFU Cache
3. Design HashMap
4. Time Based Key Value Store
5. Design Underground System
6. Logger Rate Limiter
7. First Unique Number

---

# Takeaway

```text
Store:

tokenId → expirationTime

Generate:
    Insert token

Renew:
    Update only if not expired

Count:
    Count tokens with
    expirationTime > currentTime
```

**Pattern:** Design + HashMap

**Time Complexity:**
Generate = O(1)
Renew = O(1)
Count = O(N)

**Space Complexity:** O(N)
