---

# 📌 Topic 2: Strings in Java

---

## 🔹 What is a String?

* In Java, a **String** is an **immutable sequence of characters**.
* Once created, it cannot be changed (modifications create a new object).
* Stored in the **String Pool** inside the heap for memory efficiency.

---

## 🔹 String Basics in Java

```java
String s1 = "Hello";           // string literal
String s2 = new String("Hi");  // using new keyword
System.out.println(s1.length());   // 5
System.out.println(s1.charAt(1)); // 'e'
System.out.println(s1.substring(1, 4)); // "ell"
System.out.println(s1.equals("Hello")); // true
System.out.println(s1.compareTo("World")); // negative (lexicographic compare)
```

---

## 🔹 Common Operations

* Traversal using `charAt(i)`
* Substrings
* Concatenation: `+` or `StringBuilder`
* Reversal
* Searching (Naive / KMP / Rabin-Karp)
* Anagrams & Palindromes

---

## 🔹 Time Complexity of String Operations

| Operation               | Complexity     |
| ----------------------- | -------------- |
| Access char by index    | O(1)           |
| Concatenation (String)  | O(n)           |
| Concatenation (Builder) | O(1) amortized |
| Substring               | O(k)           |
| CompareTo               | O(min(n, m))   |

---

## 🔹 LeetCode Problems on Strings

---

### 1. [LeetCode 344 - Reverse String](https://leetcode.com/problems/reverse-string/)

**Problem:** Given a character array `s`, reverse it in-place.

```java
class Solution {
    public void reverseString(char[] s) {
        int left = 0, right = s.length - 1;
        while (left < right) {
            char temp = s[left];
            s[left] = s[right];
            s[right] = temp;
            left++;
            right--;
        }
    }
}
```

✅ Complexity: O(n), O(1) space

---

### 2. [LeetCode 125 - Valid Palindrome](https://leetcode.com/problems/valid-palindrome/)

**Problem:** Given a string, return `true` if it is a palindrome considering only alphanumeric characters and ignoring cases.

```java
class Solution {
    public boolean isPalindrome(String s) {
        int left = 0, right = s.length() - 1;
        while (left < right) {
            while (left < right && !Character.isLetterOrDigit(s.charAt(left))) left++;
            while (left < right && !Character.isLetterOrDigit(s.charAt(right))) right--;
            
            if (Character.toLowerCase(s.charAt(left)) != Character.toLowerCase(s.charAt(right)))
                return false;
            
            left++;
            right--;
        }
        return true;
    }
}
```

✅ Complexity: O(n), O(1) space

---

### 3. [LeetCode 242 - Valid Anagram](https://leetcode.com/problems/valid-anagram/)

**Problem:** Given two strings, check if they are anagrams.

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) return false;

        int[] count = new int[26];
        for (char c : s.toCharArray()) count[c - 'a']++;
        for (char c : t.toCharArray()) count[c - 'a']--;

        for (int n : count) if (n != 0) return false;
        return true;
    }
}
```

✅ Complexity: O(n), O(1) space (since alphabet size fixed)

---

### 4. [LeetCode 3 - Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

**Problem:** Find length of longest substring without repeating characters.

```java
import java.util.*;

class Solution {
    public int lengthOfLongestSubstring(String s) {
        Set<Character> set = new HashSet<>();
        int left = 0, maxLen = 0;

        for (int right = 0; right < s.length(); right++) {
            while (set.contains(s.charAt(right))) {
                set.remove(s.charAt(left));
                left++;
            }
            set.add(s.charAt(right));
            maxLen = Math.max(maxLen, right - left + 1);
        }
        return maxLen;
    }
}
```

✅ Complexity: O(n), O(n) space

---

### 5. [LeetCode 28 - Find the Index of the First Occurrence in a String (strStr)](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/)

**Problem:** Implement substring search.

```java
class Solution {
    public int strStr(String haystack, String needle) {
        return haystack.indexOf(needle); // Built-in
    }
}
```
---

# 📌 Advanced String Algorithms

---

## 1. **KMP Algorithm (Knuth-Morris-Pratt)**

🔹 **Use case:** Substring search in O(n+m) time.
🔹 Idea:

* Build an **LPS (Longest Prefix Suffix)** array for the pattern.
* Use it to avoid re-checking characters when mismatch occurs.

### Example

Pattern: `"ababaca"`
LPS = `[0,0,1,2,3,0,1]`

### Java Implementation

```java
class KMP {
    public static int strStr(String text, String pattern) {
        if (pattern.isEmpty()) return 0;

        int[] lps = computeLPS(pattern);
        int i = 0, j = 0; // i -> text, j -> pattern

        while (i < text.length()) {
            if (text.charAt(i) == pattern.charAt(j)) {
                i++; j++;
                if (j == pattern.length()) return i - j; // found
            } else {
                if (j > 0) j = lps[j - 1];
                else i++;
            }
        }
        return -1;
    }

    private static int[] computeLPS(String pattern) {
        int[] lps = new int[pattern.length()];
        int len = 0, i = 1;
        while (i < pattern.length()) {
            if (pattern.charAt(i) == pattern.charAt(len)) {
                lps[i++] = ++len;
            } else {
                if (len > 0) len = lps[len - 1];
                else lps[i++] = 0;
            }
        }
        return lps;
    }

    public static void main(String[] args) {
        System.out.println(strStr("abxabcabcaby", "abcaby")); // Output: 6
    }
}
```

✅ Complexity: **O(n+m)**
📌 Related LeetCode: [28. Implement strStr()](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/)

---

## 2. **Rabin-Karp Algorithm (Rolling Hash)**

🔹 **Use case:** Multiple pattern matching, plagiarism detection.
🔹 Idea:

* Compute hash of substring & compare.
* Use rolling hash to update in O(1).
* May have collisions (handle with equality check).

### Java Implementation

```java
class RabinKarp {
    public static int rabinKarp(String text, String pattern) {
        int n = text.length(), m = pattern.length();
        if (m > n) return -1;

        int base = 256, mod = 101; // prime mod
        int pHash = 0, tHash = 0, h = 1;

        // Precompute h = pow(base, m-1) % mod
        for (int i = 0; i < m - 1; i++) {
            h = (h * base) % mod;
        }

        // Initial hash values
        for (int i = 0; i < m; i++) {
            pHash = (base * pHash + pattern.charAt(i)) % mod;
            tHash = (base * tHash + text.charAt(i)) % mod;
        }

        for (int i = 0; i <= n - m; i++) {
            if (pHash == tHash) {
                if (text.substring(i, i + m).equals(pattern)) return i;
            }

            if (i < n - m) {
                tHash = (base * (tHash - text.charAt(i) * h) + text.charAt(i + m)) % mod;
                if (tHash < 0) tHash += mod;
            }
        }
        return -1;
    }

    public static void main(String[] args) {
        System.out.println(rabinKarp("abedabc", "abc")); // Output: 4
    }
}
```

✅ Complexity: Average O(n+m), Worst O(nm) (if too many collisions)
📌 Related LeetCode: [28. Implement strStr()](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/)

---

## 3. **Z-Algorithm (Pattern Matching)**

🔹 **Use case:** Pattern matching in O(n+m).
🔹 Idea:

* Build **Z array** where `Z[i]` = length of longest prefix of string that starts at `i`.
* Use it for substring search.

### Java Implementation

```java
class ZAlgorithm {
    public static void search(String text, String pattern) {
        String concat = pattern + "$" + text;
        int n = concat.length();
        int[] Z = new int[n];
        computeZ(concat, Z);

        for (int i = 0; i < n; i++) {
            if (Z[i] == pattern.length()) {
                System.out.println("Pattern found at index " + (i - pattern.length() - 1));
            }
        }
    }

    private static void computeZ(String str, int[] Z) {
        int n = str.length();
        int L = 0, R = 0;
        for (int i = 1; i < n; i++) {
            if (i > R) {
                L = R = i;
                while (R < n && str.charAt(R - L) == str.charAt(R)) R++;
                Z[i] = R - L;
                R--;
            } else {
                int k = i - L;
                if (Z[k] < R - i + 1) {
                    Z[i] = Z[k];
                } else {
                    L = i;
                    while (R < n && str.charAt(R - L) == str.charAt(R)) R++;
                    Z[i] = R - L;
                    R--;
                }
            }
        }
    }

    public static void main(String[] args) {
        search("abxabcabcaby", "abcaby"); // Output: Pattern found at index 6
    }
}
```

✅ Complexity: **O(n+m)**
📌 Related LeetCode: \[28. Implement strStr()]

---

## 4. **Manacher’s Algorithm (Longest Palindromic Substring)**

🔹 **Use case:** Find **longest palindromic substring** in O(n).
🔹 Idea:

* Insert `#` between characters to handle even-length palindromes.
* Expand around centers with optimization.

### Java Implementation

```java
class Manachers {
    public static String longestPalindrome(String s) {
        StringBuilder sb = new StringBuilder("^");
        for (char c : s.toCharArray()) {
            sb.append("#").append(c);
        }
        sb.append("#$");

        char[] arr = sb.toString().toCharArray();
        int[] P = new int[arr.length];
        int center = 0, right = 0;

        for (int i = 1; i < arr.length - 1; i++) {
            int mirror = 2 * center - i;
            if (i < right) {
                P[i] = Math.min(right - i, P[mirror]);
            }

            while (arr[i + (1 + P[i])] == arr[i - (1 + P[i])]) {
                P[i]++;
            }

            if (i + P[i] > right) {
                center = i;
                right = i + P[i];
            }
        }

        int maxLen = 0, centerIndex = 0;
        for (int i = 1; i < P.length - 1; i++) {
            if (P[i] > maxLen) {
                maxLen = P[i];
                centerIndex = i;
            }
        }

        int start = (centerIndex - maxLen) / 2;
        return s.substring(start, start + maxLen);
    }

    public static void main(String[] args) {
        System.out.println(longestPalindrome("babad")); // Output: "bab" or "aba"
    }
}
```

✅ Complexity: **O(n)**
📌 Related LeetCode: [5. Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)

---

# 🔹 Summary

* **KMP** → Best for substring search.
* **Rabin-Karp** → Best for multiple pattern search.
* **Z-Algorithm** → Another efficient pattern matching technique.
* **Manacher’s** → Best for palindromic substring detection.

---

## 🔹 Practice More (Suggested LeetCode)

* [LeetCode 14 - Longest Common Prefix](https://leetcode.com/problems/longest-common-prefix/)
* [LeetCode 5 - Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)
* [LeetCode 151 - Reverse Words in a String](https://leetcode.com/problems/reverse-words-in-a-string/)
* [LeetCode 49 - Group Anagrams](https://leetcode.com/problems/group-anagrams/)

---
