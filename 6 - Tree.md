
---

## 🌱 **1. Fundamentals of Trees**

* A **Tree** is made of **nodes**.
* The **topmost node** is called the **root**.
* Each node may have **0 or more children**.
* Nodes with no children are called **leaf nodes**.
* The connection between nodes is called an **edge**.
* A tree with `n` nodes has exactly `n - 1` edges.

### **Types of Trees**

1. **Binary Tree** → Each node has at most **2 children** (left, right).
2. **Binary Search Tree (BST)** → A binary tree with ordering:

    * Left subtree < root
    * Right subtree > root
3. **Balanced Trees** (AVL, Red-Black Tree).
4. **Heaps** (used in Priority Queues).
5. **Trie** (used for prefix search).

We’ll start with **Binary Trees**.

---

## 🌳 **2. Binary Tree Node Implementation in Java**

```java
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    TreeNode(int x) {
        val = x;
        left = null;
        right = null;
    }
}
```

---

# 🌳 Binary Trees – Core Problems

### 1. **Maximum Depth of Binary Tree**

👉 *LeetCode #104*

**Problem:**
Given the root of a binary tree, return its maximum depth.

* A binary tree’s maximum depth = number of nodes along the longest path from the root to a leaf.

**Example:**

```
Input: root = [3,9,20,null,null,15,7]
Output: 3
```

**Approach:**

* Use **DFS recursion**:

    * Depth = `1 + max(leftDepth, rightDepth)`
    * Base case → if `root == null` return 0.

**Java Code:**

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) return 0;
        return 1 + Math.max(maxDepth(root.left), maxDepth(root.right));
    }
}
```

**Alternate Approach (BFS – Level Order):**

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) return 0;
        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);
        int depth = 0;
        while (!q.isEmpty()) {
            int size = q.size();
            for (int i = 0; i < size; i++) {
                TreeNode node = q.poll();
                if (node.left != null) q.offer(node.left);
                if (node.right != null) q.offer(node.right);
            }
            depth++;
        }
        return depth;
    }
}
```

---

### 2. **Symmetric Tree**

👉 *LeetCode #101*

**Problem:**
Check whether a binary tree is symmetric around its center.

**Example:**

```
Input: root = [1,2,2,3,4,4,3]
Output: true
```

**Approach:**

* Tree is symmetric if `left subtree` is a mirror of `right subtree`.
* Recursive check:

    * Values must match
    * `left.left == right.right` and `left.right == right.left`

**Java Code:**

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null) return true;
        return isMirror(root.left, root.right);
    }

    private boolean isMirror(TreeNode t1, TreeNode t2) {
        if (t1 == null && t2 == null) return true;
        if (t1 == null || t2 == null) return false;
        return (t1.val == t2.val) &&
               isMirror(t1.left, t2.right) &&
               isMirror(t1.right, t2.left);
    }
}
```

---

### 3. **Diameter of Binary Tree**

👉 *LeetCode #543*

**Problem:**
The diameter of a tree = length of the longest path between any 2 nodes.

**Example:**

```
Input: root = [1,2,3,4,5]
Output: 3
Explanation: [4 → 2 → 1 → 3] has 3 edges.
```

**Approach:**

* At each node → longest path through that node = `leftDepth + rightDepth`.
* Keep track of max diameter during DFS.

**Java Code:**

```java
class Solution {
    private int diameter = 0;

    public int diameterOfBinaryTree(TreeNode root) {
        depth(root);
        return diameter;
    }

    private int depth(TreeNode node) {
        if (node == null) return 0;
        int left = depth(node.left);
        int right = depth(node.right);
        diameter = Math.max(diameter, left + right);
        return 1 + Math.max(left, right);
    }
}
```

---

We’ll cover the **four core traversals**:

1. **Preorder (Root → Left → Right)**
2. **Inorder (Left → Root → Right)**
3. **Postorder (Left → Right → Root)**
4. **Level Order (BFS)**

---

## 🌳 1. Binary Tree Node Structure in Java

```java
class TreeNode {
    int val;
    TreeNode left, right;

    TreeNode(int val) {
        this.val = val;
        left = right = null;
    }
}
```

---

## 🌳 2. Preorder Traversal (DFS)

👉 **Definition**: Visit **Root → Left → Right**

### Recursive Approach

```java
class BinaryTree {
    void preorder(TreeNode root) {
        if (root == null) return;
        System.out.print(root.val + " ");
        preorder(root.left);
        preorder(root.right);
    }
}
```

### Iterative (using Stack)

```java
class BinaryTree {
    void preorderIterative(TreeNode root) {
        if (root == null) return;
        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode node = stack.pop();
            System.out.print(node.val + " ");
            if (node.right != null) stack.push(node.right);
            if (node.left != null) stack.push(node.left);
        }
    }
}
```

🔑 **LeetCode Problem:** [Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/)

---

## 🌳 3. Inorder Traversal (DFS)

👉 **Definition**: Visit **Left → Root → Right**

### Recursive

```java
class BinaryTree {
    void inorder(TreeNode root) {
        if (root == null) return;
        inorder(root.left);
        System.out.print(root.val + " ");
        inorder(root.right);
    }
}
```

### Iterative

```java
class BinaryTree {
    void inorderIterative(TreeNode root) {
        Stack<TreeNode> stack = new Stack<>();
        TreeNode curr = root;
        while (curr != null || !stack.isEmpty()) {
            while (curr != null) {
                stack.push(curr);
                curr = curr.left;
            }
            curr = stack.pop();
            System.out.print(curr.val + " ");
            curr = curr.right;
        }
    }
}
```

🔑 **LeetCode Problem:** [Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/)

---

## 🌳 4. Postorder Traversal (DFS)

👉 **Definition**: Visit **Left → Right → Root**

### Recursive

```java
class BinaryTree {
    void postorder(TreeNode root) {
        if (root == null) return;
        postorder(root.left);
        postorder(root.right);
        System.out.print(root.val + " ");
    }
}
```

### Iterative (2 stacks)

```java
class BinaryTree {
    void postorderIterative(TreeNode root) {
        if (root == null) return;
        Stack<TreeNode> stack1 = new Stack<>();
        Stack<TreeNode> stack2 = new Stack<>();
        stack1.push(root);
        while (!stack1.isEmpty()) {
            TreeNode node = stack1.pop();
            stack2.push(node);
            if (node.left != null) stack1.push(node.left);
            if (node.right != null) stack1.push(node.right);
        }
        while (!stack2.isEmpty()) {
            System.out.print(stack2.pop().val + " ");
        }
    }
}
```

🔑 **LeetCode Problem:** [Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/)

---

## 🌳 5. Level Order Traversal (BFS)

👉 **Definition**: Traverse level by level (Top → Bottom, Left → Right)

### Using Queue

```java
class BinaryTree {
    void levelOrder(TreeNode root) {
        if (root == null) return;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            TreeNode node = queue.poll();
            System.out.print(node.val + " ");
            if (node.left != null) queue.add(node.left);
            if (node.right != null) queue.add(node.right);
        }
    }
}
```

🔑 **LeetCode Problem:** [Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)

---

✅ **Summary of Traversals**

* **Preorder** → Root first
* **Inorder** → Root in middle
* **Postorder** → Root last
* **Level-order** → BFS using Queue

---
