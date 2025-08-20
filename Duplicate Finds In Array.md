###Given an integer array nums, return true if any value appears more than once in the array; otherwise, return false. 
Input: nums = [1, 2, 3, 3] 
Output: true 
Input: nums = [1, 2, 3, 4] 
Output: false

### Solution ✅

```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    public boolean hasDuplicate(int[] nums) {
        Set<Integer> seen = new HashSet<>();
        for (int num : nums) {
            if (seen.contains(num)) {
                return true; // duplicate found
            }
            seen.add(num);
        }
        return false; // no duplicates found
    }
}
```

---

## 🔹 What is a Brute Force Algorithm?

A **Brute Force Algorithm** is the simplest and most straightforward way of solving a problem:
👉 it tries **all possible solutions or comparisons** until it finds the answer.

* It doesn’t use any optimization or advanced data structures.
* It’s usually **easy to implement** but **inefficient** (slow) for large inputs.
* Often has **O(n²)** or worse time complexity.

Think of it as: *"check everything until you’re sure."*

---

## 🔹 Brute Force for This Problem (Detecting Duplicates)

We are asked: **Does the array contain duplicates?**

### Brute Force Idea:

* Compare **each element with every other element**.
* If any two are equal → return `true`.
* If no match is found after checking all → return `false`.

---

### Example

Input:

```java
nums = [1, 2, 3, 3]
```

Steps:

1. Compare `1` with `2`, `3`, `3` → no match.
2. Compare `2` with `3`, `3` → no match.
3. Compare `3` with next `3` → match found ✅ return `true`.

---

### Brute Force Implementation in Java

```java
class Solution {
    public boolean hasDuplicate(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[i] == nums[j]) {
                    return true; // duplicate found
                }
            }
        }
        return false; // no duplicates
    }
}
```

---

### 🔹 Complexity Analysis

* **Time Complexity:** `O(n²)`

  * For each element, we compare it with all others.
  * If `n = 10,000`, then \~100 million comparisons 😬.
* **Space Complexity:** `O(1)`

  * We don’t use extra memory (no HashSet, no sorting).

---

## 🔹 Brute Force vs Optimized Approach

| Approach        | Time Complexity | Space Complexity                             | Explanation                                    |
| --------------- | --------------- | -------------------------------------------- | ---------------------------------------------- |
| **Brute Force** | `O(n²)`         | `O(1)`                                       | Compares every pair. Simple, but slow.         |
| **HashSet**     | `O(n)`          | `O(n)`                                       | Uses extra memory to check duplicates quickly. |
| **Sorting**     | `O(n log n)`    | `O(1)` (or `O(n)` if sort uses extra memory) | Sorts array, then checks neighbors.            |

### Why This Implementation is Better

* **Time Complexity:**

  * Loop runs once → `O(n)` where `n` = number of elements.
  * Each set operation (`contains`, `add`) is average `O(1)` due to hashing.
* **Space Complexity:**

  * In the worst case (all elements unique), we store all `n` elements in the set → `O(n)` space.
* **Clean Logic:**

  * Early return on finding a duplicate → avoids unnecessary processing.
  * No redundant checks at the end.
  * More readable naming.

---

✅ Brute force is good for **understanding and very small inputs**.
✅ Optimized solutions (like **HashSet**) are **much faster** for large arrays.

---
