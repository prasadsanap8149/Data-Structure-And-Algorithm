---

# 📌 Topic 1: Arrays in Java

---

## 🔹 What is an Array?

* An **array** is a collection of elements of the **same type**, stored in **contiguous memory**.
* Indexing in Java arrays starts from **0**.
* Size of an array is **fixed** once created.

---

## 🔹 Why Arrays?

* Fast access (O(1)) using index.
* Useful for problems involving sequential data.
* Basis for advanced structures (strings, matrices, heaps).

---

## 🔹 Common Operations

1. **Declaration & Initialization**

   ```java
   int[] arr = new int[5];          // default values 0
   int[] nums = {10, 20, 30, 40};  // initialized
   ```

2. **Accessing Elements**

   ```java
   System.out.println(nums[2]);  // Output: 30
   ```

3. **Traversing**

   ```java
   for (int i = 0; i < nums.length; i++) {
       System.out.print(nums[i] + " ");
   }
   ```

4. **Updating**

   ```java
   nums[1] = 99;  // replace 20 with 99
   ```

5. **Insertion (manual shift since size is fixed)**

   ```java
   int[] newArr = new int[nums.length + 1];
   for (int i = 0; i < 2; i++) {
       newArr[i] = nums[i];
   }
   newArr[2] = 55; // inserted element
   for (int i = 2; i < nums.length; i++) {
       newArr[i+1] = nums[i];
   }
   ```

6. **Deletion (manual shift)**

   ```java
   int index = 2; // delete element at index 2
   for (int i = index; i < nums.length - 1; i++) {
       nums[i] = nums[i+1];
   }
   ```

---

## 🔹 Time Complexity of Array Operations

| Operation        | Time Complexity           |
| ---------------- | ------------------------- |
| Access by index  | O(1)                      |
| Update value     | O(1)                      |
| Insert at end    | O(1) (if space)           |
| Insert in middle | O(n)                      |
| Deletion         | O(n)                      |
| Search           | O(n) / O(log n) if sorted |

---

## 🔹 Example Problems (with Java code)

### 1. Reverse an Array

```java
class ReverseArray {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5};
        int start = 0, end = arr.length - 1;

        while (start < end) {
            int temp = arr[start];
            arr[start] = arr[end];
            arr[end] = temp;
            start++;
            end--;
        }

        for (int num : arr) {
            System.out.print(num + " ");
        }
    }
}
```

✅ Output: `5 4 3 2 1`

---

### 2. Find Maximum and Minimum in Array

```java
class MaxMinArray {
    public static void main(String[] args) {
        int[] arr = {12, 45, 2, 67, 34};
        int max = arr[0], min = arr[0];

        for (int i = 1; i < arr.length; i++) {
            if (arr[i] > max) max = arr[i];
            if (arr[i] < min) min = arr[i];
        }

        System.out.println("Max: " + max);
        System.out.println("Min: " + min);
    }
}
```

✅ Output:

```
Max: 67
Min: 2
```

---

### 3. Find Subarray with Maximum Sum (Kadane’s Algorithm)

```java
class MaxSubarraySum {
    public static void main(String[] args) {
        int[] arr = {-2, 1, -3, 4, -1, 2, 1, -5, 4};
        int maxSoFar = arr[0], currSum = arr[0];

        for (int i = 1; i < arr.length; i++) {
            currSum = Math.max(arr[i], currSum + arr[i]);
            maxSoFar = Math.max(maxSoFar, currSum);
        }

        System.out.println("Maximum Subarray Sum: " + maxSoFar);
    }
}
```

✅ Output: `6` (subarray `[4, -1, 2, 1]`)

---

## 🔹 Practice Problems

1. Rotate an array by `k` steps.
2. Find the second largest element in an array.
3. Check if an array is a palindrome.
4. Merge two sorted arrays.
5. Move all zeros to the end of the array.

---
