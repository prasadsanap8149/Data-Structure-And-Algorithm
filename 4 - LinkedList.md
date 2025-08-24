---

# 📌 Topic 4: Linked List in Java

---

## 🔹 1. What is a Linked List?

A **Linked List** is a linear data structure where elements (called **nodes**) are stored in memory separately, and each node contains:

* **Data** (the actual value)
* **Next** (a reference to the next node)

Unlike arrays:

* **Arrays** → Contiguous memory, fixed size.
* **Linked List** → Non-contiguous memory, dynamic size.

📌 Advantages: Dynamic size, easy insertions/deletions.
📌 Disadvantages: Extra memory for pointers, slower access (O(n) vs O(1) in arrays).

---

## 🔹 2. Structure of a Node in Java

Each **node** can be represented as a simple Java class:

```java
class Node {
    int data;
    Node next;

    Node(int data) {
        this.data = data;
        this.next = null;
    }
}
```

---

## 🔹 3. Singly Linked List Implementation

We’ll create a simple **LinkedList** class that allows:

* Insert at end
* Insert at beginning
* Print list

```java
class LinkedList {
    Node head;  // start of the list

    // Insert at end
    public void insertAtEnd(int data) {
        Node newNode = new Node(data);
        if (head == null) {
            head = newNode;
            return;
        }
        Node temp = head;
        while (temp.next != null) {
            temp = temp.next;
        }
        temp.next = newNode;
    }

    // Insert at beginning
    public void insertAtBeginning(int data) {
        Node newNode = new Node(data);
        newNode.next = head;
        head = newNode;
    }

    // Print the list
    public void printList() {
        Node temp = head;
        while (temp != null) {
            System.out.print(temp.data + " -> ");
            temp = temp.next;
        }
        System.out.println("null");
    }
}

// Driver class
public class Main {
    public static void main(String[] args) {
        LinkedList list = new LinkedList();
        list.insertAtEnd(10);
        list.insertAtEnd(20);
        list.insertAtEnd(30);
        list.insertAtBeginning(5);

        list.printList();  // Output: 5 -> 10 -> 20 -> 30 -> null
    }
}
```

---

## 🔹 4. Complexity

* Insertion at beginning → **O(1)**
* Insertion at end → **O(n)** (if no tail pointer)
* Printing (traversal) → **O(n)**

---

# 🔗 Types of Linked Lists

## 1) Singly Linked List (SLL)

### When to use

* Minimal memory overhead.
* Simple structures (stacks/queues with head/tail pointers).
* Forward-only traversal is fine.

### Node & List (Java)

```java
// Singly Linked List
class SLL {
    static class Node {
        int val;
        Node next;
        Node(int v) { this.val = v; }
    }

    private Node head, tail;
    private int size;

    // O(1)
    public void addFirst(int v) {
        Node n = new Node(v);
        n.next = head;
        head = n;
        if (tail == null) tail = n;
        size++;
    }

    // O(1) amortized with tail; O(n) if you don’t track tail
    public void addLast(int v) {
        Node n = new Node(v);
        if (tail == null) head = tail = n;
        else { tail.next = n; tail = n; }
        size++;
    }

    // O(1)
    public boolean removeFirst() {
        if (head == null) return false;
        head = head.next;
        if (head == null) tail = null;
        size--;
        return true;
    }

    // O(n)
    public boolean removeValue(int v) {
        Node prev = null, cur = head;
        while (cur != null && cur.val != v) { prev = cur; cur = cur.next; }
        if (cur == null) return false;
        if (prev == null) return removeFirst();
        prev.next = cur.next;
        if (cur == tail) tail = prev;
        size--;
        return true;
    }

    public void print() {
        Node c = head;
        while (c != null) { System.out.print(c.val + " -> "); c = c.next; }
        System.out.println("null");
    }
}
```

### Complexity (SLL)

* `addFirst`: O(1)
* `addLast`: O(1) with `tail` (otherwise O(n))
* `removeFirst`: O(1)
* `remove(value)`: O(n)
* `search`: O(n)
* Memory: 1 pointer per node

---

## 2) Doubly Linked List (DLL)

### When to use

* Need **both directions** traversal.
* Need **O(1) deletion** when you have a node reference (LRU Cache, Deque).
* Frequent insertions/removals at both ends.

### Node & List (Java)

```java
// Doubly Linked List
class DLL {
    static class Node {
        int val;
        Node prev, next;
        Node(int v) { this.val = v; }
    }

    private Node head, tail;
    private int size;

    // O(1)
    public void addFirst(int v) {
        Node n = new Node(v);
        n.next = head;
        if (head != null) head.prev = n;
        head = n;
        if (tail == null) tail = n;
        size++;
    }

    // O(1)
    public void addLast(int v) {
        Node n = new Node(v);
        n.prev = tail;
        if (tail != null) tail.next = n;
        tail = n;
        if (head == null) head = n;
        size++;
    }

    // O(1)
    public boolean removeFirst() {
        if (head == null) return false;
        head = head.next;
        if (head != null) head.prev = null; else tail = null;
        size--;
        return true;
    }

    // O(1)
    public boolean removeLast() {
        if (tail == null) return false;
        tail = tail.prev;
        if (tail != null) tail.next = null; else head = null;
        size--;
        return true;
    }

    // O(n) to find; O(1) to unlink
    public boolean removeValue(int v) {
        Node cur = head;
        while (cur != null && cur.val != v) cur = cur.next;
        if (cur == null) return false;
        unlink(cur);
        return true;
    }

    // Core unlink in O(1)
    private void unlink(Node x) {
        Node p = x.prev, n = x.next;
        if (p != null) p.next = n; else head = n;
        if (n != null) n.prev = p; else tail = p;
        size--;
    }

    public void printForward() {
        Node c = head;
        while (c != null) { System.out.print(c.val + " <-> "); c = c.next; }
        System.out.println("null");
    }

    public void printBackward() {
        Node c = tail;
        while (c != null) { System.out.print(c.val + " <-> "); c = c.prev; }
        System.out.println("null");
    }
}
```

### Complexity (DLL)

* `addFirst/addLast`: O(1)
* `removeFirst/removeLast`: O(1)
* `remove(node ref)`: O(1)
* `remove(value)`: O(n) to find
* Memory: **2 pointers** per node

---

## 3) Circular Linked List (CLL) — Singly Circular

### When to use

* **Round-robin** scheduling, **Josephus** problem, continuous looping structures.
* Efficient **O(1) addLast** with only a `tail` pointer (head = `tail.next`).

### Node & List (Java)

```java
// Circular Singly Linked List (CSLL)
class CSLL {
    static class Node {
        int val;
        Node next;
        Node(int v) { this.val = v; }
    }

    private Node tail; // if not null, head = tail.next
    private int size;

    // O(1)
    public void addFirst(int v) {
        Node n = new Node(v);
        if (tail == null) {
            tail = n;
            tail.next = tail; // points to itself
        } else {
            n.next = tail.next; // new head
            tail.next = n;
        }
        size++;
    }

    // O(1)
    public void addLast(int v) {
        addFirst(v);
        tail = tail.next; // shift tail to the new last
    }

    // O(1)
    public boolean removeFirst() {
        if (tail == null) return false;
        Node head = tail.next;
        if (head == tail) { // single node
            tail = null;
        } else {
            tail.next = head.next;
        }
        size--;
        return true;
    }

    // O(n)
    public boolean removeValue(int v) {
        if (tail == null) return false;
        Node prev = tail, cur = tail.next; // start at head
        do {
            if (cur.val == v) {
                if (cur == tail) tail = (tail == tail.next) ? null : prev;
                prev.next = cur.next;
                size--;
                return true;
            }
            prev = cur; cur = cur.next;
        } while (cur != tail.next);
        return false;
    }

    public void printOnceAround() {
        if (tail == null) { System.out.println("empty"); return; }
        Node head = tail.next, c = head;
        do {
            System.out.print(c.val + " -> ");
            c = c.next;
        } while (c != head);
        System.out.println("(back to head)");
    }
}
```

### Complexity (CSLL)

* `addFirst/addLast`: O(1)
* `removeFirst`: O(1)
* `remove(value)`: O(n)
* Memory: 1 pointer per node

> ⚠️ Always guard traversals with `do { ... } while (c != head);` to avoid infinite loops.

---

## Quick Comparison & Use-Cases

| Type | Extra Pointers  | Bidirectional | Typical Uses                                 |
| ---- | --------------- | ------------- | -------------------------------------------- |
| SLL  | 1 (`next`)      | No            | Stacks, simple queues, adjacency lists       |
| DLL  | 2 (`prev,next`) | Yes           | LRU Cache, Deque, Undo/Redo, Browser history |
| CLL  | 1 (`next`)      | No\*          | Round-robin, Josephus, repeating playlists   |

\*Circular **doubly** lists exist too (used in some LRU/Deque designs).

---

## Gotchas / Edge Cases

* **Empty & single-node** lists (especially in CLL).
* Always update **both head & tail** when removing from a 1-element list.
* In DLL, carefully update **both `prev` and `next`** when unlinking.
* Traversals in CLL must stop when you come back to **head**.

---

* Remove Nth Node From End (LC 19)
* Intersection of Two Linked Lists (LC 160)
* Palindrome Linked List (LC 234)
* Linked List Cycle II (Cycle start) (LC 142)



# 🔹 Linked List Problems (LeetCode-style)

---

## **1. Reverse a Linked List**

👉 *LeetCode 206. Reverse Linked List*

**Problem:**
Given the head of a singly linked list, reverse the list, and return the new head.

**Example:**

```
Input: 1 → 2 → 3 → 4 → 5 → NULL
Output: 5 → 4 → 3 → 2 → 1 → NULL
```

**Solution (Iterative - Java):**

```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode prev = null;
        ListNode curr = head;

        while (curr != null) {
            ListNode nextTemp = curr.next; // store next node
            curr.next = prev;              // reverse pointer
            prev = curr;                   // move prev forward
            curr = nextTemp;               // move curr forward
        }
        return prev;
    }
}
```

✅ **Time Complexity:** O(n)
✅ **Space Complexity:** O(1)

---

## **2. Detect Cycle in a Linked List**

👉 *LeetCode 141. Linked List Cycle*

**Problem:**
Given `head`, determine if the linked list has a cycle in it.

**Floyd’s Cycle Detection Algorithm (Tortoise & Hare):**

* Use two pointers (`slow` and `fast`).
* `slow` moves 1 step, `fast` moves 2 steps.
* If they meet → cycle exists.

**Solution (Java):**

```java
class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null || head.next == null) return false;

        ListNode slow = head;
        ListNode fast = head.next;

        while (slow != fast) {
            if (fast == null || fast.next == null) return false;
            slow = slow.next;
            fast = fast.next.next;
        }
        return true;
    }
}
```

✅ **Time Complexity:** O(n)
✅ **Space Complexity:** O(1)

---

## **3. Merge Two Sorted Linked Lists**

👉 *LeetCode 21. Merge Two Sorted Lists*

**Problem:**
Merge two sorted linked lists and return it as a new sorted list.

**Example:**

```
List1: 1 → 2 → 4
List2: 1 → 3 → 4
Output: 1 → 1 → 2 → 3 → 4 → 4
```

**Solution (Java):**

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(-1);
        ListNode current = dummy;

        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                current.next = l1;
                l1 = l1.next;
            } else {
                current.next = l2;
                l2 = l2.next;
            }
            current = current.next;
        }

        if (l1 != null) current.next = l1;
        if (l2 != null) current.next = l2;

        return dummy.next;
    }
}
```

✅ **Time Complexity:** O(m + n)
✅ **Space Complexity:** O(1)

---

## ✅ Next Few Important Linked List Problems:

* LeetCode 19 → Remove Nth Node From End
* LeetCode 160 → Intersection of Two Linked Lists
* LeetCode 234 → Palindrome Linked List
* LeetCode 142 → Linked List Cycle II (return starting node of cycle)

---
