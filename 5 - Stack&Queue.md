
# 📌 Stack in DSA

A **Stack** is a linear data structure that follows **LIFO (Last In, First Out)**.
Think of it like a stack of plates 🍽️ — you can only add/remove from the top.

**Main Operations:**

* `push(x)` → insert element at top
* `pop()` → remove top element
* `peek()` → see top element without removing
* `isEmpty()` → check if stack is empty

In Java, we can use:

* `java.util.Stack<T>`
* `Deque<T>` (preferred in real-world since `Stack` is legacy)

---

## 🔹 Basic Implementation (with Array)

```java
class MyStack {
    private int[] arr;
    private int top;
    private int capacity;

    public MyStack(int size) {
        arr = new int[size];
        capacity = size;
        top = -1;
    }

    public void push(int x) {
        if (top == capacity - 1) throw new RuntimeException("Stack Overflow");
        arr[++top] = x;
    }

    public int pop() {
        if (isEmpty()) throw new RuntimeException("Stack Underflow");
        return arr[top--];
    }

    public int peek() {
        if (isEmpty()) throw new RuntimeException("Stack Empty");
        return arr[top];
    }

    public boolean isEmpty() {
        return top == -1;
    }
}
```

---

# 📌 LeetCode-style Stack Problems

Now let’s dive into **classic problems** you’ll encounter.

---

### **1. Valid Parentheses** (LeetCode #20)

👉 Check if a string of brackets `()[]{}` is valid.

```java
import java.util.*;

class Solution {
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();
        for (char c : s.toCharArray()) {
            if (c == '(') stack.push(')');
            else if (c == '{') stack.push('}');
            else if (c == '[') stack.push(']');
            else if (stack.isEmpty() || stack.pop() != c) return false;
        }
        return stack.isEmpty();
    }
}
```

**Key Idea:**

* Push expected closing brackets
* Pop when matching bracket appears
* Fail if mismatch or stack not empty

---

### **2. Min Stack** (LeetCode #155)

👉 Design a stack that supports `push`, `pop`, `top`, and retrieving the **minimum** in O(1).

```java
class MinStack {
    private Stack<Integer> stack = new Stack<>();
    private Stack<Integer> minStack = new Stack<>();

    public void push(int x) {
        stack.push(x);
        if (minStack.isEmpty() || x <= minStack.peek()) {
            minStack.push(x);
        }
    }

    public void pop() {
        if (stack.pop().equals(minStack.peek())) {
            minStack.pop();
        }
    }

    public int top() {
        return stack.peek();
    }

    public int getMin() {
        return minStack.peek();
    }
}
```

---

### **3. Daily Temperatures** (LeetCode #739)

👉 For each day, find how many days until a warmer temperature.

```java
import java.util.*;

class Solution {
    public int[] dailyTemperatures(int[] temps) {
        int n = temps.length;
        int[] res = new int[n];
        Stack<Integer> stack = new Stack<>();
        
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && temps[i] > temps[stack.peek()]) {
                int idx = stack.pop();
                res[idx] = i - idx;
            }
            stack.push(i);
        }
        return res;
    }
}
```

**Key Idea:**

* Use a **monotonic stack** (decreasing order)
* Pop when a warmer day comes

---

### **4. Largest Rectangle in Histogram** (LeetCode #84)

👉 Find largest rectangle area in histogram.

```java
import java.util.*;

class Solution {
    public int largestRectangleArea(int[] heights) {
        Stack<Integer> stack = new Stack<>();
        int maxArea = 0;
        int n = heights.length;
        
        for (int i = 0; i <= n; i++) {
            int h = (i == n ? 0 : heights[i]);
            while (!stack.isEmpty() && h < heights[stack.peek()]) {
                int height = heights[stack.pop()];
                int width = stack.isEmpty() ? i : i - stack.peek() - 1;
                maxArea = Math.max(maxArea, height * width);
            }
            stack.push(i);
        }
        return maxArea;
    }
}
```

---

### **5. Evaluate Reverse Polish Notation** (LeetCode #150)

👉 Evaluate math expression in postfix notation.

```java
import java.util.*;

class Solution {
    public int evalRPN(String[] tokens) {
        Stack<Integer> stack = new Stack<>();
        for (String t : tokens) {
            if ("+-*/".contains(t)) {
                int b = stack.pop();
                int a = stack.pop();
                switch (t) {
                    case "+": stack.push(a + b); break;
                    case "-": stack.push(a - b); break;
                    case "*": stack.push(a * b); break;
                    case "/": stack.push(a / b); break;
                }
            } else {
                stack.push(Integer.parseInt(t));
            }
        }
        return stack.pop();
    }
}
```

---
Great choice 🚀 Let’s now cover **Queues in depth**. Queues are another fundamental data structure (after Stack and Linked List) and are heavily used in **BFS, scheduling, caching, sliding window problems, and heaps**.

We’ll cover them step by step:

---

## 🔹 1. Queue Basics (FIFO)
- **Definition:** Queue works on **First In, First Out (FIFO)** principle.
- **Operations:**
    - `enqueue(x)` → Add element at the rear.
    - `dequeue()` → Remove element from the front.
    - `peek()` → Get front element.
    - `isEmpty()` → Check if empty.

### Java Implementation (using Array)
```java
class Queue {
    private int[] arr;
    private int front, rear, size, capacity;

    public Queue(int capacity) {
        this.capacity = capacity;
        arr = new int[capacity];
        front = 0;
        size = 0;
        rear = capacity - 1;
    }

    public boolean isEmpty() { return size == 0; }
    public boolean isFull() { return size == capacity; }

    public void enqueue(int x) {
        if (isFull()) {
            System.out.println("Queue is full");
            return;
        }
        rear = (rear + 1) % capacity;
        arr[rear] = x;
        size++;
    }

    public int dequeue() {
        if (isEmpty()) throw new RuntimeException("Queue is empty");
        int item = arr[front];
        front = (front + 1) % capacity;
        size--;
        return item;
    }

    public int peek() {
        if (isEmpty()) throw new RuntimeException("Queue is empty");
        return arr[front];
    }
}
```

---

## 🔹 2. Circular Queue
- Optimized queue where unused space at front is reused.
- Uses **modulus operator (`%`)** to wrap around.

✅ Already implemented above (`rear = (rear+1) % capacity`).

---

## 🔹 3. Deque (Double-Ended Queue)
- Can add/remove elements from **both front and rear**.
- Used in **sliding window maximum** problems.

### Java Example (using `ArrayDeque`)
```java
import java.util.*;

public class DequeExample {
    public static void main(String[] args) {
        Deque<Integer> dq = new ArrayDeque<>();
        dq.addFirst(1);
        dq.addLast(2);
        dq.addFirst(0);
        System.out.println(dq); // [0, 1, 2]

        dq.removeLast();
        System.out.println(dq); // [0, 1]
    }
}
```

---

## 🔹 4. Priority Queue (Heap-based Queue)
- Elements are served based on **priority** instead of order.
- Implemented using **Heap** internally in Java.

### Java Example
```java
import java.util.*;

public class PriorityQueueExample {
    public static void main(String[] args) {
        // Min-Heap (default)
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        minHeap.add(5);
        minHeap.add(1);
        minHeap.add(3);
        System.out.println(minHeap.poll()); // 1 (smallest)

        // Max-Heap
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        maxHeap.add(5);
        maxHeap.add(1);
        maxHeap.add(3);
        System.out.println(maxHeap.poll()); // 5 (largest)
    }
}
```

---
 📌 LeetCode-style Queue Problems
---

## 1) Implement Queue using Stacks — LC 232

**Idea:** Two stacks: `in` for pushes, `out` for pops/peeks. Shift only when needed.

```java
class MyQueue {
    private Deque<Integer> in = new ArrayDeque<>();
    private Deque<Integer> out = new ArrayDeque<>();

    public void push(int x) { in.push(x); }

    public int pop() { move(); return out.pop(); }

    public int peek() { move(); return out.peek(); }

    public boolean empty() { return in.isEmpty() && out.isEmpty(); }

    private void move() {
        if (out.isEmpty())
            while (!in.isEmpty()) out.push(in.pop());
    }
}
```

⏱ Amortized O(1).

---

## 2) Implement Stack using Queues — LC 225

**Idea:** One queue. Push then rotate `size-1` to simulate stack top at front.

```java
class MyStack {
    private Queue<Integer> q = new ArrayDeque<>();

    public void push(int x) {
        q.offer(x);
        for (int i = 0; i < q.size() - 1; i++) q.offer(q.poll());
    }
    public int pop() { return q.poll(); }
    public int top() { return q.peek(); }
    public boolean empty() { return q.isEmpty(); }
}
```

⏱ Push O(n), others O(1).

---

## 3) Design Circular Queue — LC 622

```java
class MyCircularQueue {
    private final int[] a;
    private int head = 0, tail = -1, size = 0;

    public MyCircularQueue(int k) { a = new int[k]; }

    public boolean enQueue(int v) {
        if (isFull()) return false;
        tail = (tail + 1) % a.length;
        a[tail] = v; size++; return true;
    }
    public boolean deQueue() {
        if (isEmpty()) return false;
        head = (head + 1) % a.length; size--; return true;
    }
    public int Front() { return isEmpty() ? -1 : a[head]; }
    public int Rear() { return isEmpty() ? -1 : a[tail]; }
    public boolean isEmpty() { return size == 0; }
    public boolean isFull() { return size == a.length; }
}
```

---

## 4) Sliding Window Maximum — LC 239 (Deque / Monotonic Queue)

**Idea:** Deque holds **indices** of decreasing values.

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int n = nums.length; if (n == 0) return new int[0];
        int[] res = new int[n - k + 1];
        Deque<Integer> dq = new ArrayDeque<>();

        for (int i = 0; i < n; i++) {
            while (!dq.isEmpty() && dq.peekFirst() <= i - k) dq.pollFirst();
            while (!dq.isEmpty() && nums[dq.peekLast()] <= nums[i]) dq.pollLast();
            dq.offerLast(i);
            if (i >= k - 1) res[i - k + 1] = nums[dq.peekFirst()];
        }
        return res;
    }
}
```

⏱ O(n).

---

## 5) Number of Recent Calls — LC 933

**Idea:** Queue of timestamps; pop older than `t-3000`.

```java
class RecentCounter {
    private Deque<Integer> q = new ArrayDeque<>();
    public int ping(int t) {
        q.offerLast(t);
        while (q.peekFirst() < t - 3000) q.pollFirst();
        return q.size();
    }
}
```

---

## 6) Rotting Oranges — LC 994 (BFS / Multi-source queue)

```java
class Solution {
    public int orangesRotting(int[][] grid) {
        int m = grid.length, n = grid[0].length, fresh = 0, minutes = 0;
        Deque<int[]> q = new ArrayDeque<>();
        for (int i = 0; i < m; i++)
            for (int j = 0; j < n; j++)
                if (grid[i][j] == 2) q.offer(new int[]{i, j});
                else if (grid[i][j] == 1) fresh++;

        int[][] dirs = {{1,0},{-1,0},{0,1},{0,-1}};
        while (!q.isEmpty() && fresh > 0) {
            int sz = q.size(); minutes++;
            for (int s = 0; s < sz; s++) {
                int[] p = q.poll();
                for (int[] d : dirs) {
                    int x = p[0] + d[0], y = p[1] + d[1];
                    if (0 <= x && x < m && 0 <= y && y < n && grid[x][y] == 1) {
                        grid[x][y] = 2; fresh--; q.offer(new int[]{x, y});
                    }
                }
            }
        }
        return fresh == 0 ? minutes : -1;
    }
}
```

---

## 7) Course Schedule — LC 207 (Topological Sort / BFS)

```java
class Solution {
    public boolean canFinish(int numCourses, int[][] prereq) {
        List<List<Integer>> g = new ArrayList<>();
        for (int i = 0; i < numCourses; i++) g.add(new ArrayList<>());
        int[] indeg = new int[numCourses];
        for (int[] p : prereq) { g.get(p[1]).add(p[0]); indeg[p[0]]++; }

        Deque<Integer> q = new ArrayDeque<>();
        for (int i = 0; i < numCourses; i++) if (indeg[i] == 0) q.offer(i);

        int taken = 0;
        while (!q.isEmpty()) {
            int u = q.poll(); taken++;
            for (int v : g.get(u)) if (--indeg[v] == 0) q.offer(v);
        }
        return taken == numCourses;
    }
}
```

---

## 8) Shortest Path in Binary Matrix — LC 1091 (BFS in 8 dirs)

```java
class Solution {
    public int shortestPathBinaryMatrix(int[][] g) {
        int n = g.length;
        if (g[0][0] == 1 || g[n-1][n-1] == 1) return -1;
        Deque<int[]> q = new ArrayDeque<>();
        boolean[][] vis = new boolean[n][n];
        q.offer(new int[]{0,0}); vis[0][0] = true;
        int[][] d = {{1,0},{-1,0},{0,1},{0,-1},{1,1},{1,-1},{-1,1},{-1,-1}};
        int dist = 1;
        while (!q.isEmpty()) {
            for (int sz = q.size(); sz > 0; sz--) {
                int[] p = q.poll();
                int x = p[0], y = p[1];
                if (x == n-1 && y == n-1) return dist;
                for (int[] t : d) {
                    int nx = x + t[0], ny = y + t[1];
                    if (0 <= nx && nx < n && 0 <= ny && ny < n && !vis[nx][ny] && g[nx][ny] == 0) {
                        vis[nx][ny] = true; q.offer(new int[]{nx, ny});
                    }
                }
            }
            dist++;
        }
        return -1;
    }
}
```

---

## 9) Open the Lock — LC 752 (BFS on states)

```java
class Solution {
    public int openLock(String[] deadends, String target) {
        Set<String> dead = new HashSet<>(Arrays.asList(deadends));
        if (dead.contains("0000")) return -1;
        Deque<String> q = new ArrayDeque<>();
        Set<String> vis = new HashSet<>();
        q.offer("0000"); vis.add("0000");
        int steps = 0;

        while (!q.isEmpty()) {
            for (int sz = q.size(); sz > 0; sz--) {
                String cur = q.poll();
                if (cur.equals(target)) return steps;
                if (dead.contains(cur)) continue;
                for (int i = 0; i < 4; i++) {
                    for (int dir : new int[]{-1, 1}) {
                        char[] ch = cur.toCharArray();
                        int d = (ch[i] - '0' + dir + 10) % 10;
                        ch[i] = (char)('0' + d);
                        String nxt = new String(ch);
                        if (vis.add(nxt)) q.offer(nxt);
                    }
                }
            }
            steps++;
        }
        return -1;
    }
}
```

---

## 10) K Closest Points to Origin — LC 973 (Max-Heap of size k)

```java
class Solution {
    public int[][] kClosest(int[][] points, int k) {
        PriorityQueue<int[]> pq = new PriorityQueue<>(
            (a, b) -> Integer.compare(dist(b), dist(a))  // max-heap by distance
        );
        for (int[] p : points) {
            pq.offer(p);
            if (pq.size() > k) pq.poll();
        }
        int[][] res = new int[k][2];
        for (int i = k - 1; i >= 0; i--) res[i] = pq.poll();
        return res;
    }
    private int dist(int[] p) { return p[0]*p[0] + p[1]*p[1]; }
}
```

---

## 11) Last Stone Weight — LC 1046 (Max-Heap)

```java
class Solution {
    public int lastStoneWeight(int[] stones) {
        PriorityQueue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());
        for (int s : stones) pq.offer(s);
        while (pq.size() > 1) {
            int a = pq.poll(), b = pq.poll();
            if (a != b) pq.offer(a - b);
        }
        return pq.isEmpty() ? 0 : pq.peek();
    }
}
```

---

## 12) Merge k Sorted Lists — LC 23 (Min-Heap)

```java
class ListNode { int val; ListNode next; ListNode() {} ListNode(int v){val=v;} }

class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        PriorityQueue<ListNode> pq = new PriorityQueue<>(Comparator.comparingInt(n -> n.val));
        for (ListNode n : lists) if (n != null) pq.offer(n);
        ListNode dummy = new ListNode(0), cur = dummy;
        while (!pq.isEmpty()) {
            ListNode n = pq.poll();
            cur.next = n; cur = cur.next;
            if (n.next != null) pq.offer(n.next);
        }
        return dummy.next;
    }
}
```

---

## 13) Find Median from Data Stream — LC 295 (Two Heaps)

```java
class MedianFinder {
    private PriorityQueue<Integer> low = new PriorityQueue<>(Collections.reverseOrder()); // max-heap
    private PriorityQueue<Integer> high = new PriorityQueue<>(); // min-heap

    public void addNum(int num) {
        if (low.isEmpty() || num <= low.peek()) low.offer(num); else high.offer(num);
        if (low.size() > high.size() + 1) high.offer(low.poll());
        else if (high.size() > low.size()) low.offer(high.poll());
    }

    public double findMedian() {
        if (low.size() == high.size()) return (low.peek() + high.peek()) / 2.0;
        return low.peek();
    }
}
```

---

### Bonus (Advanced Deque):

* **Shortest Subarray with Sum ≥ K — LC 862** (prefix sums + monotonic deque)
* **Sliding Window Median — LC 480** (two heaps with lazy delete)

---
