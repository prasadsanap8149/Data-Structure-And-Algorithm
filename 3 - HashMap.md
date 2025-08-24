---

# 📌 Topic 3: Hashing in Java

---

## 🔹 What is Hashing?

* **Hashing** = Mapping data to a **fixed-size value (hash code)** for fast retrieval.
* In DSA, we mostly use **HashMap, HashSet** (or HashTable).

👉 Why?

* Direct access in **O(1) average case**.
* Efficient for **lookups, counting, frequency problems, duplicates detection**.

---

## 🔹 Java Hashing Data Structures

1. **HashMap\<K,V>**

    * Stores key-value pairs.
    * Example:

      ```java
      Map<String, Integer> map = new HashMap<>();
      map.put("apple", 2);
      map.put("banana", 5);
      System.out.println(map.get("apple")); // 2
      ```
    * Keys are unique.
    * Null key allowed (only one).

2. **HashSet<E>**

    * Stores unique elements.
    * Example:

      ```java
      Set<Integer> set = new HashSet<>();
      set.add(1); set.add(2); set.add(1);
      System.out.println(set); // [1, 2]
      ```

3. **LinkedHashMap** → maintains insertion order.

4. **TreeMap** → sorted keys (O(log n)).

---

## 🔹 Common Use Cases

* Frequency counting.
* Detect duplicates.
* Lookup in O(1).
* Subarray problems (prefix sums).
* Two-sum, anagram grouping.

---

## 🔹 LeetCode Problems with Hashing

---

### 1. [LeetCode 1 - Two Sum](https://leetcode.com/problems/two-sum/)

**Problem:** Find indices of two numbers that add up to target.

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            if (map.containsKey(complement)) {
                return new int[]{map.get(complement), i};
            }
            map.put(nums[i], i);
        }
        return new int[]{};
    }
}
```

✅ Complexity: O(n)

---

### 2. [LeetCode 217 - Contains Duplicate](https://leetcode.com/problems/contains-duplicate/)

**Problem:** Return true if array contains duplicate.

```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) {
            if (!set.add(num)) return true;
        }
        return false;
    }
}
```

✅ Complexity: O(n)

---

### 3. [LeetCode 347 - Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)

**Problem:** Return `k` most frequent elements.

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> freq = new HashMap<>();
        for (int num : nums) freq.put(num, freq.getOrDefault(num, 0) + 1);

        PriorityQueue<Map.Entry<Integer, Integer>> heap =
            new PriorityQueue<>((a, b) -> b.getValue() - a.getValue());

        heap.addAll(freq.entrySet());

        int[] res = new int[k];
        for (int i = 0; i < k; i++) {
            res[i] = heap.poll().getKey();
        }
        return res;
    }
}
```

✅ Complexity: O(n log n)

---

### 4. [LeetCode 49 - Group Anagrams](https://leetcode.com/problems/group-anagrams/)

**Problem:** Group words that are anagrams.

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<>();
        for (String s : strs) {
            char[] arr = s.toCharArray();
            Arrays.sort(arr);
            String key = new String(arr);
            map.computeIfAbsent(key, x -> new ArrayList<>()).add(s);
        }
        return new ArrayList<>(map.values());
    }
}
```

✅ Complexity: O(n \* k log k), where k = word length

---

### 5. [LeetCode 560 - Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/)

**Problem:** Find number of subarrays that sum to k.

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, 1);

        int count = 0, sum = 0;
        for (int num : nums) {
            sum += num;
            if (map.containsKey(sum - k)) {
                count += map.get(sum - k);
            }
            map.put(sum, map.getOrDefault(sum, 0) + 1);
        }
        return count;
    }
}
```

✅ Complexity: O(n)

---

## 🔹 More Hashing Problems to Practice

* [LeetCode 36 - Valid Sudoku](https://leetcode.com/problems/valid-sudoku/)
* [LeetCode 128 - Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/)
* [LeetCode 383 - Ransom Note](https://leetcode.com/problems/ransom-note/)
* [LeetCode 451 - Sort Characters By Frequency](https://leetcode.com/problems/sort-characters-by-frequency/)

---

✅ With Hashing, you’ll notice **many string/array problems reduce to map-based frequency counting**.

---