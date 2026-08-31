# PHASE 3 — Custom Data Structures + Trees & Graphs
### (Parts 9 + 10 fused: Node classes → Binary Tree → Graph representations → BFS/DFS)

Trees and Graphs are often the scariest topics for beginners because you have to deal with custom `Node` classes instead of built-in arrays. But the code for traversing them is highly standardized. Once you memorize the boilerplate, these problems become much easier.

---

## 1. Custom Node Classes

**The Concept:** Trees, Graphs, and Linked Lists don't exist as built-in data structures in Java. You (or LeetCode) must define a `Node` class that holds a value and points to other nodes.

**Basic Syntax (Linked List & Binary Tree):**
```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int val) { this.val = val; }
}

class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int val) { this.val = val; }
}
```

---

## 2. Binary Tree Traversals

**The Concept:** Because a tree branches out, you can't just use a simple `for` loop. You use **Recursion (DFS)** to go deep, or a **Queue (BFS)** to go level-by-level.

### DFS (Depth First Search) - Recursive Pattern
**Basic Syntax:**
```java
public void dfs(TreeNode node) {
    if (node == null) return; // BASE CASE: Always the first line!
    
    // PRE-ORDER: Process node here (e.g., System.out.print(node.val))
    dfs(node.left);
    // IN-ORDER: Process node here (Gives sorted order in a BST!)
    dfs(node.right);
    // POST-ORDER: Process node here
}
```

### BFS (Breadth First Search) - Level Order Pattern
**The Concept:** Use an `ArrayDeque` as a queue. Process nodes one level at a time. The trick is taking a "snapshot" of the queue's size before processing the level.

**DSA Application (Printing level by level):**
```java
public void bfs(TreeNode root) {
    if (root == null) return;
    
    Deque<TreeNode> queue = new ArrayDeque<>();
    queue.offer(root);
    
    while (!queue.isEmpty()) {
        int levelSize = queue.size(); // SNAPSHOT: How many nodes are on this level?
        
        for (int i = 0; i < levelSize; i++) {
            TreeNode node = queue.poll(); // Remove from front
            System.out.print(node.val + " ");
            
            if (node.left != null) queue.offer(node.left); // Add children to back
            if (node.right != null) queue.offer(node.right);
        }
        System.out.println(); // Move to next line for the next level
    }
}
```

> [!IMPORTANT]
> **The `levelSize` Snapshot**: Without `int levelSize = queue.size();`, you won't know where one level ends and the next begins, because you are constantly adding new children to the queue!

---

## 3. Graph Representations (Adjacency List)

**The Concept:** A Graph is just a network of nodes connected by edges. In coding interviews, you are usually given a 2D array of edges, e.g., `[[0,1], [0,2], [1,2]]`. You must convert this into an **Adjacency List** before you can traverse it.

**Basic Syntax (Building the Adjacency List):**
An Adjacency List is usually a List of Lists (or an Array of Lists).

```java
int n = 3; // Number of nodes (0 to 2)
int[][] edges = {{0,1}, {0,2}, {1,2}};

// 1. Initialize the Adjacency List
List<List<Integer>> adj = new ArrayList<>();
for (int i = 0; i < n; i++) {
    adj.add(new ArrayList<>()); // Empty list for each node
}

// 2. Populate it
for (int[] edge : edges) {
    int u = edge[0];
    int v = edge[1];
    
    adj.get(u).add(v); // u points to v
    adj.get(v).add(u); // v points to u (Omit this line if directed!)
}
```

---

## 4. Graph BFS & DFS

**The Concept:** Graph traversal is exactly like Tree traversal, with one major difference: **Graphs can have cycles!** If you don't track which nodes you've visited, you will get stuck in an infinite loop.

### Graph BFS (Shortest Path in Unweighted Graph)
**DSA Application:**
```java
public void graphBFS(int startNode, List<List<Integer>> adj, int n) {
    boolean[] visited = new boolean[n]; // Track visited nodes!
    Deque<Integer> queue = new ArrayDeque<>();
    
    queue.offer(startNode);
    visited[startNode] = true; // Mark visited IMMEDIATELY when enqueuing
    
    while (!queue.isEmpty()) {
        int node = queue.poll();
        System.out.println("Visited: " + node);
        
        for (int neighbor : adj.get(node)) {
            if (!visited[neighbor]) {
                visited[neighbor] = true; 
                queue.offer(neighbor);
            }
        }
    }
}
```

> [!WARNING]
> **Common Pitfall**: Always mark a node as `visited` the exact moment you put it into the queue. If you wait until you take it *out* of the queue, multiple other nodes might add it to the queue again, causing duplicates and massive memory spikes!

### Graph DFS (Connectivity / Path Finding)
**DSA Application:**
```java
public void dfs(int node, List<List<Integer>> adj, boolean[] visited) {
    visited[node] = true;
    System.out.println("Visited: " + node);
    
    for (int neighbor : adj.get(node)) {
        if (!visited[neighbor]) {
            dfs(neighbor, adj, visited);
        }
    }
}
```

**Decision Rule:** Need the shortest path? → BFS. Need to explore all paths, detect cycles, or check if two nodes are connected? → DFS.

---

## 🚀 Next Steps
Can you open a blank editor and write the **Tree BFS (Level Order)** loop and the **Graph Adjacency List Builder** purely from memory? Try it!
