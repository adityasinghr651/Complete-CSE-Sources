# 📚 Day 10: Binary Trees — Complete Mastery Guide (Enhanced Edition for Students)

## SECTION 1: COMPLETE DEFINITIONS & CORE CONCEPTS (For Students)

### What is a Tree? 🤔

#### **Definition (Official):**

> **A Tree** is a **hierarchical data structure** consisting of **nodes** connected by **edges**. It has:
> - **No cycles** (can't go back to same node)
> - **One root node** (top of the tree)
> - **Each node has exactly one parent** (except root)
> - **Each node can have multiple children**
>
> It's like a **real tree** turned upside down — root at top, leaves at bottom!

**Key Properties:**

| Property | Description | Why Important |
|----------|-------------|---------------|
| **Root** | Topmost node | Entry point to tree |
| **Parent** | Node above | Each node has 1 parent (except root) |
| **Child** | Node below | Each node can have multiple children |
| **Leaf** | Node with no children | End of tree |
| **Edge** | Connection between nodes | Defines structure |
| **Path** | Sequence of nodes | Route from one node to another |
| **Depth** | Distance from root | Level of node |
| **Height** | Distance to farthest leaf | Tree size |

**Visual Diagram:**

```text
        A          ← Root (Depth 0, Height 3)
       / \
      B   C        ← Internal nodes (Depth 1, Height 2)
     / \   \
    D   E   F      ← Internal nodes (Depth 2, Height 1)
   /         \
  G           H    ← Leaf nodes (Depth 3, Height 0)

Terminology:
- Root: A (no parent)
- Parent of D: B
- Children of B: D, E
- Leaf: G, H (no children)
- Internal: A, B, C, D, E, F (have children)
```

---

## SECTION 2: COMPLETE NOTES MAKING POINTS (For Students)

### 📝 **Essential Notes for Exam/Interviews**

#### **Point 1: BINARY TREE (Definition & Types)**

**Definition:**

> **A Binary Tree** is a tree where **each node has a maximum of 2 children** (called **left child** and **right child**).

**Key Characteristics:**

| Property | Description | Example |
|----------|-------------|---------|
| **Maximum 2 children** | Each node can have 0, 1, or 2 children | Left child, Right child |
| **Left child** | Points to left subtree | `node.left` |
| **Right child** | Points to right subtree | `node.right` |
| **Empty** | No nodes (root = null) | Empty tree |
| **Single node** | Only root node | One node tree |

**Visual:**

```text
Binary Tree:
        1
       / \
      2   3
     / \   \
    4   5   6

Each node has 0, 1, or 2 children ✓

NOT Binary Tree:
        1
       /|\
      2 3 4

Node 1 has 3 children ❌ (not binary)
```

**Types of Binary Trees:**

| Type | Definition | Example |
|------|------------|---------|
| **Full Binary Tree** | Every node has 0 or 2 children | All nodes have 2 or 0 children |
| **Complete Binary Tree** | All levels full except last, last filled left-to-right | Heap structure |
| **Perfect Binary Tree** | All levels full, all leaves at same level | Symmetric tree |
| **Balanced Binary Tree** | Left and right subtree heights differ by at most 1 | AVL Tree |
| **Skewed Binary Tree** | All nodes have only left or right child | Linked list-like |

**Visual:**

```text
Full Binary Tree:
    1
   / \
  2   3
 /\   /\

Complete Binary Tree:
    1
   / \
  2   3
 /\   /

Perfect Binary Tree:
    1
   / \
  2   3
 /\   /\
(All levels full)

Skewed Left (Left child only):
    1
   /
  2
 /
3
```

#### **Point 2: TREE IMPLEMENTATION (Node Structure)**

**Node Structure:**

```java
class TreeNode {
    int data;           // Data field
    TreeNode left;      // Pointer to left child
    TreeNode right;     // Pointer to right child
    
    // Constructor
    TreeNode(int data) {
        this.data = data;
        this.left = null;
        this.right = null;
    }
}
```

**C++ Node:**

```cpp
struct TreeNode {
    int data;           // Data field
    TreeNode* left;     // Pointer to left child
    TreeNode* right;    // Pointer to right child
    
    // Constructor
    TreeNode(int val) {
        data = val;
        left = nullptr;
        right = nullptr;
    }
};
```

**Creating a Binary Tree:**

```java
// Create nodes
TreeNode root = new TreeNode(1);
TreeNode node2 = new TreeNode(2);
TreeNode node3 = new TreeNode(3);
TreeNode node4 = new TreeNode(4);
TreeNode node5 = new TreeNode(5);

// Connect nodes
root.left = node2;
root.right = node3;
node2.left = node4;
node2.right = node5;

// Result:
//     1
//    / \
//   2   3
//  / \
// 4   5
```

**Key Terminology:**

| Keyword | Meaning | Example |
|---------|---------|---------|
| **TreeNode** | Node class | `new TreeNode(data)` |
| **Root** | Top node | `root` |
| **Left** | Left child pointer | `node.left` |
| **Right** | Right child pointer | `node.right` |
| **Null** | No child | `node.left = null` |
| **Leaf** | Both children null | `node.left == null && node.right == null` |
| **Internal** | Has at least one child | `node.left != null || node.right != null` |
| **Depth** | Distance from root | `depth = 0` at root |
| **Height** | Distance to farthest leaf | `height = 0` at leaf |
| **Subtree** | Tree within tree | `node.left` forms subtree |

#### **Point 3: TIME COMPLEXITY TABLE**

| Operation | Binary Tree | BST | Balanced BST | Explanation |
|-----------|-------------|-----|--------------|-------------|
| **Access by Level** | O(n) | O(n) | O(log n) | Must traverse |
| **Search** | O(n) | O(n) | O(log n) | May visit all nodes |
| **Insert** | O(n) | O(n) | O(log n) | Find position + insert |
| **Delete** | O(n) | O(n) | O(log n) | Find + delete + rebalance |
| **Traversal** | O(n) | O(n) | O(n) | Visit every node |
| **Find Min/Max** | O(n) | O(n) | O(log n) | May need to traverse all |

**Why O(n) for Binary Tree?**

```text
Binary Tree:
        1
       / \
      2   3
     / \
    4   5

Find node 5:
Must visit: 1 → 2 → 5 (at least 3 nodes)
Worst case: Visit ALL nodes = O(n)
```

**Why O(log n) for Balanced BST?**

```text
Balanced BST:
        4
       / \
      2   6
     / \ / \
    1  3 5  7

Find node 7:
4 → 6 → 7 (only 3 nodes)
Tree height = log n
Time: O(log n)
```

#### **Point 4: TREE TRAVERSAL (ALL TYPES)**

**Definition:**

> **Tree Traversal** means **visiting every node** in the tree exactly once, in a specific order.

**Two Main Categories:**

| Category | Description | Types |
|----------|-------------|-------|
| **Breadth First Search (BFS)** | Level-by-level traversal | Level Order |
| **Depth First Search (DFS)** | Branch-by-branch traversal | Preorder, Inorder, Postorder |

**BFS (Level Order):**

```text
Level Order Traversal:
        1
       / \
      2   3
     / \   \
    4   5   6

Order: 1, 2, 3, 4, 5, 6
(Top to bottom, left to right)

Queue: [1]
Queue: [2, 3]
Queue: [3, 4, 5]
Queue: [4, 5, 6]
Queue: [5, 6]
Queue: [6]
Queue: []
```

**DFS (3 Types):**

| Type | Order | When to use |
|------|-------|-------------|
| **Preorder** | Root → Left → Right | Copy tree, prefix expression |
| **Inorder** | Left → Root → Right | BST inorder = sorted |
| **Postorder** | Left → Right → Root | Delete tree, postfix expression |

**Visual:**

```text
Tree:
    1
   / \
  2   3
 /\   /\
4  5 6  7

Preorder (Root → Left → Right): 1, 2, 4, 5, 3, 6, 7
Inorder (Left → Root → Right): 4, 2, 5, 1, 6, 3, 7
Postorder (Left → Right → Root): 4, 5, 2, 6, 7, 3, 1

Level Order: 1, 2, 3, 4, 5, 6, 7
```

#### **Point 5: DFS TRAVERSAL CODE**

**Preorder (Root → Left → Right):**

**Recursive:**
```java
public void preorderRecursive(TreeNode root) {
    if (root == null) return;
    
    System.out.print(root.data + " ");  // Visit root
    preorderRecursive(root.left);       // Traverse left
    preorderRecursive(root.right);      // Traverse right
}
```

**Iterative (Stack):**
```java
public void preorderIterative(TreeNode root) {
    if (root == null) return;
    
    Stack<TreeNode> stack = new Stack<>();
    stack.push(root);
    
    while (!stack.isEmpty()) {
        TreeNode curr = stack.pop();
        System.out.print(curr.data + " ");
        
        if (curr.right != null) stack.push(curr.right);
        if (curr.left != null) stack.push(curr.left);
    }
}
```

**Time**: O(n) | **Space**: O(n) (stack)

**Inorder (Left → Root → Right):**

**Recursive:**
```java
public void inorderRecursive(TreeNode root) {
    if (root == null) return;
    
    inorderRecursive(root.left);        // Traverse left
    System.out.print(root.data + " ");  // Visit root
    inorderRecursive(root.right);       // Traverse right
}
```

**Iterative (Stack):**
```java
public void inorderIterative(TreeNode root) {
    Stack<TreeNode> stack = new Stack<>();
    TreeNode curr = root;
    
    while (curr != null || !stack.isEmpty()) {
        while (curr != null) {
            stack.push(curr);
            curr = curr.left;  // Go left
        }
        
        curr = stack.pop();   // Backtrack
        System.out.print(curr.data + " ");  // Visit
        curr = curr.right;    // Go right
    }
}
```

**Time**: O(n) | **Space**: O(n) (stack)

**Postorder (Left → Right → Root):**

**Recursive:**
```java
public void postorderRecursive(TreeNode root) {
    if (root == null) return;
    
    postorderRecursive(root.left);        // Traverse left
    postorderRecursive(root.right);       // Traverse right
    System.out.print(root.data + " ");    // Visit root
}
```

**Iterative (2 Stacks):**
```java
public void postorderIterative(TreeNode root) {
    Stack<TreeNode> stack1 = new Stack<>();
    Stack<TreeNode> stack2 = new Stack<>();
    
    if (root == null) return;
    
    stack1.push(root);
    
    while (!stack1.isEmpty()) {
        TreeNode curr = stack1.pop();
        stack2.push(curr);
        
        if (curr.left != null) stack1.push(curr.left);
        if (curr.right != null) stack1.push(curr.right);
    }
    
    while (!stack2.isEmpty()) {
        System.out.print(stack2.pop().data + " ");
    }
}
```

**Time**: O(n) | **Space**: O(n) (stack)

#### **Point 6: BFS (LEVEL ORDER) TRAVERSAL**

**Algorithm:**

```text
Step 1: Create queue
Queue<TreeNode> queue = new LinkedList<>();

Step 2: Add root
queue.offer(root);

Step 3: Process level by level
while (!queue.isEmpty()) {
    int size = queue.size();  // Number of nodes at this level
    
    for (int i = 0; i < size; i++) {
        TreeNode curr = queue.poll();  // Remove from queue
        System.out.print(curr.data + " ");  // Visit
        
        if (curr.left != null) queue.offer(curr.left);
        if (curr.right != null) queue.offer(curr.right);
    }
    
    System.out.println();  // New line for each level
}

Time: O(n) | Space: O(n) (queue)
```

---

## SECTION 3: 10 MAIN BINARY TREE PATTERNS (For Students)

### Pattern 1: Maximum Depth (Height)

**When to use:**
- Find height of tree
- Check if balanced

**Template:**
```java
public int maxDepth(TreeNode root) {
    if (root == null) return 0;
    
    int leftHeight = maxDepth(root.left);
    int rightHeight = maxDepth(root.right);
    
    return Math.max(leftHeight, rightHeight) + 1;
}
```

**Time:** O(n) | **Space**: O(h) (recursion stack)

### Pattern 2: Path Sum

**When to use:**
- Check if path sum equals target
- Find all paths with sum

**Template:**
```java
public boolean hasPathSum(TreeNode root, int targetSum) {
    if (root == null) return false;
    
    if (root.left == null && root.right == null) {
        return root.val == targetSum;  // Leaf node
    }
    
    return hasPathSum(root.left, targetSum - root.val) ||
           hasPathSum(root.right, targetSum - root.val);
}
```

**Time:** O(n) | **Space**: O(h) (recursion stack)

### Pattern 3: Invert Binary Tree

**When to use:**
- Mirror image of tree
- Flip left ↔ right

**Template:**
```java
public TreeNode invertTree(TreeNode root) {
    if (root == null) return null;
    
    // Swap left and right
    TreeNode temp = root.left;
    root.left = root.right;
    root.right = temp;
    
    // Recurse
    invertTree(root.left);
    invertTree(root.right);
    
    return root;
}
```

**Time:** O(n) | **Space**: O(h) (recursion stack)

### Pattern 4: Same Tree

**When to use:**
- Check if two trees are identical
- Equal structure + values

**Template:**
```java
public boolean isSameTree(TreeNode p, TreeNode q) {
    if (p == null && q == null) return true;
    if (p == null || q == null) return false;
    if (p.val != q.val) return false;
    
    return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
}
```

**Time:** O(n) | **Space**: O(h) (recursion stack)

### Pattern 5: Symmetric Tree

**When to use:**
- Check if tree is mirror of itself
- Left subtree = Mirror of right subtree

**Template:**
```java
public boolean isSymmetric(TreeNode root) {
    return isMirror(root.left, root.right);
}

private boolean isMirror(TreeNode t1, TreeNode t2) {
    if (t1 == null && t2 == null) return true;
    if (t1 == null || t2 == null) return false;
    
    return (t1.val == t2.val)
        && isMirror(t1.left, t2.right)   // Mirror positions
        && isMirror(t1.right, t2.left);
}
```

**Time:** O(n) | **Space**: O(h) (recursion stack)

### Pattern 6: Diameter of Binary Tree

**When to use:**
- Find longest path between any two nodes
- May or may not pass through root

**Template:**
```java
int maxDiameter = 0;

public int diameterOfBinaryTree(TreeNode root) {
    dfs(root);
    return maxDiameter;
}

private int dfs(TreeNode node) {
    if (node == null) return 0;
    
    int leftDepth = dfs(node.left);
    int rightDepth = dfs(node.right);
    
    // Diameter through this node
    maxDiameter = Math.max(maxDiameter, leftDepth + rightDepth);
    
    // Return height of this subtree
    return Math.max(leftDepth, rightDepth) + 1;
}
```

**Time:** O(n) | **Space**: O(h) (recursion stack)

### Pattern 7: Level Order Traversal

**When to use:**
- BFS traversal
- Return values level by level

**Template:**
```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    
    if (root == null) return result;
    
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    
    while (!queue.isEmpty()) {
        int size = queue.size();
        List<Integer> level = new ArrayList<>();
        
        for (int i = 0; i < size; i++) {
            TreeNode curr = queue.poll();
            level.add(curr.val);
            
            if (curr.left != null) queue.offer(curr.left);
            if (curr.right != null) queue.offer(curr.right);
        }
        
        result.add(level);
    }
    
    return result;
}
```

**Time:** O(n) | **Space**: O(n) (queue)

### Pattern 8: Construct Tree from Inorder + Preorder

**When to use:**
- Build tree from traversal arrays
- Recursive construction

**Template:**
```java
int preorderIndex = 0;
Map<Integer, Integer> inorderIndexMap = new HashMap<>();

public TreeNode buildTree(int[] preorder, int[] inorder) {
    for (int i = 0; i < inorder.length; i++) {
        inorderIndexMap.put(inorder[i], i);
    }
    
    return buildHelper(preorder, 0, preorder.length - 1);
}

private TreeNode buildHelper(int[] preorder, int left, int right) {
    if (left > right) return null;
    
    int rootVal = preorder[preorderIndex++];
    TreeNode root = new TreeNode(rootVal);
    
    int inorderIndex = inorderIndexMap.get(rootVal);
    
    root.left = buildHelper(preorder, left, inorderIndex - 1);
    root.right = buildHelper(preorder, inorderIndex + 1, right);
    
    return root;
}
```

**Time**: O(n) | **Space**: O(n) (map + recursion)

### Pattern 9: Lowest Common Ancestor (LCA)

**When to use:**
- Find LCA of two nodes
- Common ancestor at lowest depth

**Template:**
```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null || root == p || root == q) return root;
    
    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);
    
    if (left != null && right != null) return root;  // p and q in different subtrees
    return left != null ? left : right;
}
```

**Time**: O(n) | **Space**: O(h) (recursion)

### Pattern 10: Serialize and Deserialize Tree

**When to use:**
- Convert tree to string
- Reconstruct from string

**Template:**
```java
// Serialize
public String serialize(TreeNode root) {
    StringBuilder sb = new StringBuilder();
    serializeHelper(root, sb);
    return sb.toString();
}

private void serializeHelper(TreeNode node, StringBuilder sb) {
    if (node == null) {
        sb.append("#,");
        return;
    }
    
    sb.append(node.val).append(",");
    serializeHelper(node.left, sb);
    serializeHelper(node.right, sb);
}

// Deserialize
public TreeNode deserialize(String data) {
    Queue<String> queue = new LinkedList<>(Arrays.asList(data.split(",")));
    return deserializeHelper(queue);
}

private TreeNode deserializeHelper(Queue<String> queue) {
    String val = queue.poll();
    if (val.equals("#")) return null;
    
    TreeNode node = new TreeNode(Integer.parseInt(val));
    node.left = deserializeHelper(queue);
    node.right = deserializeHelper(queue);
    
    return node;
}
```

**Time**: O(n) | **Space**: O(n) (string + recursion)

---

## SECTION 4: PROBLEM PROGRESSION — ALL LINKS WORKING ✓

### LeetCode Problems

#### Level 1: Very Easy

#### **1. Maximum Depth of Binary Tree**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/maximum-depth-of-binary-tree/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 10/10

**Solution:**
```java
public int maxDepth(TreeNode root) {
    if (root == null) return 0;
    return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
}
```

**Time**: O(n) | **Space**: O(h)

---

#### **2. Same Tree**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/same-tree/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 9/10

**Solution:**
```java
public boolean isSameTree(TreeNode p, TreeNode q) {
    if (p == null && q == null) return true;
    if (p == null || q == null) return false;
    if (p.val != q.val) return false;
    return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
}
```

**Time**: O(n) | **Space**: O(h)

---

#### **3. Symmetric Tree**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/symmetric-tree/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 10/10

**Solution:**
```java
public boolean isSymmetric(TreeNode root) {
    return isMirror(root.left, root.right);
}

private boolean isMirror(TreeNode t1, TreeNode t2) {
    if (t1 == null && t2 == null) return true;
    if (t1 == null || t2 == null) return false;
    
    return (t1.val == t2.val)
        && isMirror(t1.left, t2.right)
        && isMirror(t1.right, t2.left);
}
```

**Time**: O(n) | **Space**: O(h)

---

#### Level 2: Easy

#### **4. Invert Binary Tree**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/invert-binary-tree/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 10/10

**Solution:**
```java
public TreeNode invertTree(TreeNode root) {
    if (root == null) return null;
    
    TreeNode temp = root.left;
    root.left = root.right;
    root.right = temp;
    
    invertTree(root.left);
    invertTree(root.right);
    
    return root;
}
```

**Time**: O(n) | **Space**: O(h)

---

#### **5. Path Sum**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/path-sum/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 9/10

**Solution:**
```java
public boolean hasPathSum(TreeNode root, int targetSum) {
    if (root == null) return false;
    
    if (root.left == null && root.right == null) {
        return root.val == targetSum;
    }
    
    return hasPathSum(root.left, targetSum - root.val) ||
           hasPathSum(root.right, targetSum - root.val);
}
```

**Time**: O(n) | **Space**: O(h)

---

#### **6. Balanced Binary Tree**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/balanced-binary-tree/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 9/10

**Solution:**
```java
public boolean isBalanced(TreeNode root) {
    return checkHeight(root) != -1;
}

private int checkHeight(TreeNode node) {
    if (node == null) return 0;
    
    int left = checkHeight(node.left);
    if (left == -1) return -1;
    
    int right = checkHeight(node.right);
    if (right == -1) return -1;
    
    if (Math.abs(left - right) > 1) return -1;
    
    return Math.max(left, right) + 1;
}
```

**Time**: O(n) | **Space**: O(h)

---

#### **7. Binary Tree Paths**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/binary-tree-paths/
- **Company Tags**: Microsoft, Amazon
- **Frequency**: 7/10

**Solution:**
```java
public List<String> binaryTreePaths(TreeNode root) {
    List<String> result = new ArrayList<>();
    dfs(root, "", result);
    return result;
}

private void dfs(TreeNode node, String path, List<String> result) {
    if (node == null) return;
    
    path += node.val;
    
    if (node.left == null && node.right == null) {
        result.add(path);
        return;
    }
    
    dfs(node.left, path + "->", result);
    dfs(node.right, path + "->", result);
}
```

**Time**: O(n) | **Space**: O(h)

---

#### Level 3: Medium

#### **8. Binary Tree Level Order Traversal**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/binary-tree-level-order-traversal/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 10/10

**Solution:**
```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    
    if (root == null) return result;
    
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    
    while (!queue.isEmpty()) {
        int size = queue.size();
        List<Integer> level = new ArrayList<>();
        
        for (int i = 0; i < size; i++) {
            TreeNode curr = queue.poll();
            level.add(curr.val);
            
            if (curr.left != null) queue.offer(curr.left);
            if (curr.right != null) queue.offer(curr.right);
        }
        
        result.add(level);
    }
    
    return result;
}
```

**Time**: O(n) | **Space**: O(n)

---

#### **9. Binary Tree Zigzag Level Order**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 10/10

**Solution:**
```java
public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    
    if (root == null) return result;
    
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    boolean leftToRight = true;
    
    while (!queue.isEmpty()) {
        int size = queue.size();
        List<Integer> level = new ArrayList<>();
        
        for (int i = 0; i < size; i++) {
            TreeNode curr = queue.poll();
            
            if (leftToRight) {
                level.add(curr.val);
            } else {
                level.add(0, curr.val);
            }
            
            if (curr.left != null) queue.offer(curr.left);
            if (curr.right != null) queue.offer(curr.right);
        }
        
        result.add(level);
        leftToRight = !leftToRight;
    }
    
    return result;
}
```

**Time**: O(n) | **Space**: O(n)

---

#### **10. Construct Binary Tree from Preorder and Inorder Traversal**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 10/10

**Solution:**
```java
int preorderIndex = 0;
Map<Integer, Integer> inorderIndexMap = new HashMap<>();

public TreeNode buildTree(int[] preorder, int[] inorder) {
    for (int i = 0; i < inorder.length; i++) {
        inorderIndexMap.put(inorder[i], i);
    }
    
    return buildHelper(preorder, 0, preorder.length - 1);
}

private TreeNode buildHelper(int[] preorder, int left, int right) {
    if (left > right) return null;
    
    int rootVal = preorder[preorderIndex++];
    TreeNode root = new TreeNode(rootVal);
    
    int inorderIndex = inorderIndexMap.get(rootVal);
    
    root.left = buildHelper(preorder, left, inorderIndex - 1);
    root.right = buildHelper(preorder, inorderIndex + 1, right);
    
    return root;
}
```

**Time**: O(n) | **Space**: O(n)

---

#### **11. Lowest Common Ancestor of a Binary Tree**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 10/10

**Solution:**
```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null || root == p || root == q) return root;
    
    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);
    
    if (left != null && right != null) return root;
    return left != null ? left : right;
}
```

**Time**: O(n) | **Space**: O(h)

---

#### **12. Diameter of Binary Tree**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/diameter-of-binary-tree/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 10/10

**Solution:**
```java
int maxDiameter = 0;

public int diameterOfBinaryTree(TreeNode root) {
    dfs(root);
    return maxDiameter;
}

private int dfs(TreeNode node) {
    if (node == null) return 0;
    
    int leftDepth = dfs(node.left);
    int rightDepth = dfs(node.right);
    
    maxDiameter = Math.max(maxDiameter, leftDepth + rightDepth);
    
    return Math.max(leftDepth, rightDepth) + 1;
}
```

**Time**: O(n) | **Space**: O(h)

---

#### **13. Validate Binary Search Tree**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/validate-binary-search-tree/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 10/10

**Solution:**
```java
public boolean isValidBST(TreeNode root) {
    return validate(root, null, null);
}

private boolean validate(TreeNode node, Integer min, Integer max) {
    if (node == null) return true;
    
    if ((min != null && node.val <= min) || (max != null && node.val >= max)) {
        return false;
    }
    
    return validate(node.left, min, node.val) &&
           validate(node.right, node.val, max);
}
```

**Time**: O(n) | **Space**: O(h)

---

#### **14. Serialize and Deserialize Binary Tree**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/serialize-and-deserialize-binary-tree/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 10/10

**Solution:**
```java
public class Codec {
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        serializeHelper(root, sb);
        return sb.toString();
    }
    
    private void serializeHelper(TreeNode node, StringBuilder sb) {
        if (node == null) {
            sb.append("#,");
            return;
        }
        
        sb.append(node.val).append(",");
        serializeHelper(node.left, sb);
        serializeHelper(node.right, sb);
    }
    
    public TreeNode deserialize(String data) {
        Queue<String> queue = new LinkedList<>(Arrays.asList(data.split(",")));
        return deserializeHelper(queue);
    }
    
    private TreeNode deserializeHelper(Queue<String> queue) {
        String val = queue.poll();
        if (val.equals("#")) return null;
        
        TreeNode node = new TreeNode(Integer.parseInt(val));
        node.left = deserializeHelper(queue);
        node.right = deserializeHelper(queue);
        
        return node;
    }
}
```

**Time**: O(n) | **Space**: O(n)

---

#### **15. All Nodes Distance K in Binary Tree**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 9/10

**Solution:**
```java
public List<Integer> distanceK(TreeNode root, TreeNode target, int k) {
    Map<TreeNode, TreeNode> parent = new HashMap<>();
    dfsForParent(root, null, parent);
    
    Queue<TreeNode> queue = new LinkedList<>();
    Set<TreeNode> visited = new HashSet<>();
    queue.offer(target);
    visited.add(target);
    
    int distance = 0;
    
    while (!queue.isEmpty() && distance < k) {
        int size = queue.size();
        
        for (int i = 0; i < size; i++) {
            TreeNode curr = queue.poll();
            
            if (curr.left != null && !visited.contains(curr.left)) {
                queue.offer(curr.left);
                visited.add(curr.left);
            }
            
            if (curr.right != null && !visited.contains(curr.right)) {
                queue.offer(curr.right);
                visited.add(curr.right);
            }
            
            TreeNode p = parent.get(curr);
            if (p != null && !visited.contains(p)) {
                queue.offer(p);
                visited.add(p);
            }
        }
        
        distance++;
    }
    
    List<Integer> result = new ArrayList<>();
    for (TreeNode node : queue) {
        result.add(node.val);
    }
    
    return result;
}

private void dfsForParent(TreeNode node, TreeNode p, Map<TreeNode, TreeNode> parent) {
    if (node == null) return;
    
    parent.put(node, p);
    dfsForParent(node.left, node, parent);
    dfsForParent(node.right, node, parent);
}
```

**Time**: O(n) | **Space**: O(n)

---

#### Level 4: Hard

#### **16. Binary Tree Maximum Path Sum**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/binary-tree-maximum-path-sum/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 10/10

**Solution:**
```java
int maxSum = Integer.MIN_VALUE;

public int maxPathSum(TreeNode root) {
    dfs(root);
    return maxSum;
}

private int dfs(TreeNode node) {
    if (node == null) return 0;
    
    int left = Math.max(dfs(node.left), 0);
    int right = Math.max(dfs(node.right), 0);
    
    int currentMax = node.val + left + right;
    maxSum = Math.max(maxSum, currentMax);
    
    return node.val + Math.max(left, right);
}
```

**Time**: O(n) | **Space**: O(h)

---

#### **17. Merge K Sorted Lists**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/merge-k-sorted-lists/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 10/10

**Solution:**
```java
public ListNode mergeKLists(ListNode[] lists) {
    if (lists == null || lists.length == 0) return null;
    
    PriorityQueue<ListNode> minHeap = new PriorityQueue<>((a, b) -> a.val - b.val);
    
    for (ListNode list : lists) {
        if (list != null) minHeap.offer(list);
    }
    
    ListNode dummy = new ListNode(0);
    ListNode curr = dummy;
    
    while (!minHeap.isEmpty()) {
        ListNode node = minHeap.poll();
        curr.next = node;
        curr = curr.next;
        
        if (node.next != null) minHeap.offer(node.next);
    }
    
    return dummy.next;
}
```

**Time**: O(n log k) | **Space**: O(k)

---

#### **18. Binary Tree Right Side View**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/binary-tree-right-side-view/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 9/10

**Solution:**
```java
public List<Integer> rightSideView(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    
    if (root == null) return result;
    
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    
    while (!queue.isEmpty()) {
        int size = queue.size();
        
        for (int i = 0; i < size; i++) {
            TreeNode curr = queue.poll();
            
            if (i == size - 1) {  // Last node in level
                result.add(curr.val);
            }
            
            if (curr.left != null) queue.offer(curr.left);
            if (curr.right != null) queue.offer(curr.right);
        }
    }
    
    return result;
}
```

**Time**: O(n) | **Space**: O(n)

---

### CodeChef Problems

#### **19. Binary Tree Height**
- **Platform**: CodeChef
- **Link**: https://www.codechef.com/learn/course/data-structures/trees (search "Binary Tree")
- **Rating**: 800

Practice DFS/BFS operations.

---

### Codeforces Problems

#### **20. Tree Traversal Problems**
- **Platform**: Codeforces
- **Link**: https://codeforces.com/problemset?tags=trees
- **Rating**: 1000-1200

Practice tree traversal, LCA, diameter problems.

---

## SECTION 5: LeetCode Top 20 (ALL WORKING LINKS) ✓

### Easy (7 problems)

| # | Problem | Link |
|---|---------|------|
| 1 | Maximum Depth of Binary Tree | https://leetcode.com/problems/maximum-depth-of-binary-tree/ |
| 2 | Same Tree | https://leetcode.com/problems/same-tree/ |
| 3 | Symmetric Tree | https://leetcode.com/problems/symmetric-tree/ |
| 4 | Invert Binary Tree | https://leetcode.com/problems/invert-binary-tree/ |
| 5 | Path Sum | https://leetcode.com/problems/path-sum/ |
| 6 | Balanced Binary Tree | https://leetcode.com/problems/balanced-binary-tree/ |
| 7 | Binary Tree Paths | https://leetcode.com/problems/binary-tree-paths/ |

### Medium (8 problems)

| # | Problem | Link |
|---|---------|------|
| 1 | Binary Tree Level Order Traversal | https://leetcode.com/problems/binary-tree-level-order-traversal/ |
| 2 | Binary Tree Zigzag Level Order | https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/ |
| 3 | Construct Binary Tree from Preorder and Inorder | https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/ |
| 4 | Lowest Common Ancestor of Binary Tree | https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/ |
| 5 | Diameter of Binary Tree | https://leetcode.com/problems/diameter-of-binary-tree/ |
| 6 | Validate Binary Search Tree | https://leetcode.com/problems/validate-binary-search-tree/ |
| 7 | Serialize and Deserialize Binary Tree | https://leetcode.com/problems/serialize-and-deserialize-binary-tree/ |
| 8 | Binary Tree Right Side View | https://leetcode.com/problems/binary-tree-right-side-view/ |

### Hard (5 problems)

| # | Problem | Link |
|---|---------|------|
| 1 | Binary Tree Maximum Path Sum | https://leetcode.com/problems/binary-tree-maximum-path-sum/ |
| 2 | Serialize and Deserialize Binary Tree | https://leetcode.com/problems/serialize-and-deserialize-binary-tree/ |
| 3 | All Nodes Distance K in Binary Tree | https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree/ |
| 4 | Merge K Sorted Lists | https://leetcode.com/problems/merge-k-sorted-lists/ |
| 5 | Binary Tree Cameras | https://leetcode.com/problems/binary-tree-cameras/ |

---

## SECTION 6: Active Learning — 5 Questions

1. **Maximum Depth of Binary Tree** — LeetCode 104  
   [Link](https://leetcode.com/problems/maximum-depth-of-binary-tree/)

2. **Same Tree** — LeetCode 100  
   [Link](https://leetcode.com/problems/same-tree/)

3. **Invert Binary Tree** — LeetCode 226  
   [Link](https://leetcode.com/problems/invert-binary-tree/)

4. **Binary Tree Level Order Traversal** — LeetCode 102  
   [Link](https://leetcode.com/problems/binary-tree-level-order-traversal/)

5. **Diameter of Binary Tree** — LeetCode 543  
   [Link](https://leetcode.com/problems/diameter-of-binary-tree/)

---

**Submit your 5 solutions in Java!**

---

## SECTION 7: Revision Notes — CHEAT SHEET

### 📝 **Binary Tree Cheat Sheet**

| Concept | Java Code | C++ Code | Time |
|---------|-----------|----------|------|
| **Create Node** | `new TreeNode(data)` | `new TreeNode(data)` | O(1) |
| **Preorder** | `preorder(root)` | `preorder(root)` | O(n) |
| **Inorder** | `inorder(root)` | `inorder(root)` | O(n) |
| **Postorder** | `postorder(root)` | `postorder(root)` | O(n) |
| **Level Order** | `levelOrder(root)` | `levelOrder(root)` | O(n) |
| **Max Depth** | `maxDepth(root)` | `maxDepth(root)` | O(n) |
| **Invert Tree** | `invertTree(root)` | `invertTree(root)` | O(n) |
| **LCA** | `lowestCommonAncestor(root, p, q)` | `lowestCommonAncestor(root, p, q)` | O(n) |
| **Diameter** | `diameterOfBinaryTree(root)` | `diameterOfBinaryTree(root)` | O(n) |
| **Validate BST** | `isValidBST(root)` | `isValidBST(root)` | O(n) |

**Key Patterns:**
```java
// DFS (Recursive)
public void dfs(TreeNode node) {
    if (node == null) return;
    
    // Preorder: Visit root
    System.out.println(node.val);
    
    dfs(node.left);
    dfs(node.right);
    
    // Postorder: Visit root
    // System.out.println(node.val);
}

// BFS (Level Order)
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    if (root == null) return result;
    
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    
    while (!queue.isEmpty()) {
        int size = queue.size();
        List<Integer> level = new ArrayList<>();
        
        for (int i = 0; i < size; i++) {
            TreeNode curr = queue.poll();
            level.add(curr.val);
            
            if (curr.left != null) queue.offer(curr.left);
            if (curr.right != null) queue.offer(curr.right);
        }
        
        result.add(level);
    }
    
    return result;
}

// Max Depth
public int maxDepth(TreeNode root) {
    if (root == null) return 0;
    return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
}

// LCA
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null || root == p || root == q) return root;
    
    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);
    
    if (left != null && right != null) return root;
    return left != null ? left : right;
}

// Diameter
int maxDiameter = 0;
public int diameterOfBinaryTree(TreeNode root) {
    dfs(root);
    return maxDiameter;
}

private int dfs(TreeNode node) {
    if (node == null) return 0;
    
    int left = dfs(node.left);
    int right = dfs(node.right);
    
    maxDiameter = Math.max(maxDiameter, left + right);
    
    return Math.max(left, right) + 1;
}
```

---

## SECTION 8: KEYWORDS & FUNCTIONS SUMMARY

### 🔑 **ALL KEYWORDS**

| Keyword | Type | Purpose |
|---------|------|---------|
| **TreeNode** | Class/Struct | Node unit |
| **root** | Variable | First node |
| **left** | Field | Left child |
| **right** | Field | Right child |
| **null** | Value | No child |
| **leaf** | Condition | Both children null |
| **internal** | Condition | Has children |
| **depth** | Value | Distance from root |
| **height** | Value | Distance to farthest leaf |
| **subtree** | Concept | Tree within tree |
| **preorder** | Traversal | Root → Left → Right |
| **inorder** | Traversal | Left → Root → Right |
| **postorder** | Traversal | Left → Right → Root |
| **level order** | Traversal | BFS |
| **LCA** | Concept | Lowest Common Ancestor |
| **diameter** | Concept | Longest path |
| **BST** | Type | Binary Search Tree |
| **balanced** | Type | Height diff ≤ 1 |

### ⚙️ **ALL FUNCTIONS**

| Function | Purpose | Time |
|----------|---------|------|
| `maxDepth(root)` | Find height | O(n) |
| `isSameTree(p, q)` | Check identical | O(n) |
| `isSymmetric(root)` | Check mirror | O(n) |
| `invertTree(root)` | Flip tree | O(n) |
| `hasPathSum(root, target)` | Check path sum | O(n) |
| `isBalanced(root)` | Check balanced | O(n) |
| `binaryTreePaths(root)` | All paths | O(n) |
| `levelOrder(root)` | BFS traversal | O(n) |
| `zigzagLevelOrder(root)` | Zigzag BFS | O(n) |
| `buildTree(preorder, inorder)` | Construct tree | O(n) |
| `lowestCommonAncestor(root, p, q)` | Find LCA | O(n) |
| `diameterOfBinaryTree(root)` | Find diameter | O(n) |
| `isValidBST(root)` | Validate BST | O(n) |
| `serialize(root)` | Tree to string | O(n) |
| `deserialize(data)` | String to tree | O(n) |
| `distanceK(root, target, k)` | Nodes at distance k | O(n) |
| `maxPathSum(root)` | Max path sum | O(n) |
| `rightSideView(root)` | Right view | O(n) |

---
