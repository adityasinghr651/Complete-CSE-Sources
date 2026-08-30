# PHASE 3 — Custom Data Structures + Trees & Graphs
### (Parts 9 + 10 fused: Node classes → Binary Tree → Graph representations → BFS/DFS/Dijkstra/Topo Sort)

---

## 1. Why custom classes first

Most tree/graph/linked-list problems hand you a `Node` class or expect you to define one. The goal: never freeze when a problem needs a custom structure.

**Generic pattern for any node-based structure:**
```java
class Node {
    int val;
    Node next;      // linked list
    Node(int val) { this.val = val; }
}
```

---

## 2. Linked List Node

```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int val) { this.val = val; }
}
```
**Traversal:**
```java
ListNode cur = head;
while (cur != null) {
    // process cur.val
    cur = cur.next;
}
```
**Reversal (the pattern you must know cold):**
```java
ListNode prev = null, cur = head;
while (cur != null) {
    ListNode next = cur.next;  // save before overwriting
    cur.next = prev;
    prev = cur;
    cur = next;
}
return prev; // new head
```
**Complexity**: O(n) time, O(1) space.

---

## 3. Binary Tree Node

```java
class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int val) { this.val = val; }
}
```

### Recursive traversals (the shape you'll reuse for every tree problem)
```java
void preorder(TreeNode node) {
    if (node == null) return;
    // process node.val (root first)
    preorder(node.left);
    preorder(node.right);
}

void inorder(TreeNode node) {
    if (node == null) return;
    inorder(node.left);
    // process node.val (sorted order for a BST)
    inorder(node.right);
}

void postorder(TreeNode node) {
    if (node == null) return;
    postorder(node.left);
    postorder(node.right);
    // process node.val (children first — used for deletion, height calc)
}
```
**Base case is always `node == null → return`.** This is the #1 thing people forget under pressure.

### Iterative DFS (using ArrayDeque as a stack)
```java
Deque<TreeNode> stack = new ArrayDeque<>();
stack.push(root);
while (!stack.isEmpty()) {
    TreeNode node = stack.pop();
    // process node.val
    if (node.right != null) stack.push(node.right); // push right first
    if (node.left != null) stack.push(node.left);   // so left pops first
}
```

### BFS / Level order (using ArrayDeque as a queue)
```java
Deque<TreeNode> queue = new ArrayDeque<>();
queue.offer(root);
while (!queue.isEmpty()) {
    int levelSize = queue.size();       // snapshot — this is the key trick for level-by-level
    for (int i = 0; i < levelSize; i++) {
        TreeNode node = queue.poll();
        // process node.val
        if (node.left != null) queue.offer(node.left);
        if (node.right != null) queue.offer(node.right);
    }
    // one full level processed here
}
```
**The `levelSize` snapshot is the single most important trick in level-order traversal** — without it you can't tell where one level ends and the next begins.

### BST-specific
```java
TreeNode search(TreeNode node, int target) {
    if (node == null || node.val == target) return node;
    return target < node.val ? search(node.left, target) : search(node.right, target);
}
```
**Decision rule**: "sorted order traversal of a BST" → inorder gives you sorted output for free.

---

## 4. Graph representations

**Adjacency list — the default choice for almost everything:**
```java
// Unweighted, node labels 0..n-1
List<List<Integer>> adj = new ArrayList<>();
for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
adj.get(u).add(v);
adj.get(v).add(u); // omit this line if directed
```

**HashMap-based graph** — use when node labels aren't clean 0..n-1 integers (e.g. Strings):
```java
Map<String, List<String>> adj = new HashMap<>();
adj.computeIfAbsent(u, k -> new ArrayList<>()).add(v);
```
(`computeIfAbsent` is the HashMap idiom for "create the list if it's not there yet, then use it" — very common in graph-building code.)

**Adjacency matrix** — only when the graph is dense or n is small (≤ ~1000):
```java
int[][] adjMatrix = new int[n][n]; // adjMatrix[u][v] = weight, or 1/0 for unweighted
```

**Weighted edges** — pair each neighbor with a weight, usually as `int[]{neighbor, weight}`:
```java
List<List<int[]>> adj = new ArrayList<>();
for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
adj.get(u).add(new int[]{v, weight});
```

**Decision rule**: "I need each node's neighbors" → adjacency list (ArrayList-of-ArrayLists for int labels, HashMap-of-Lists for non-integer labels). Reach for a matrix only when explicitly dense/small.

---

## 5. Graph traversal — BFS and DFS

**Visited tracking**: use a `boolean[] visited` (if labels are 0..n-1) or `HashSet<T>` (if labels aren't clean integers). Always mark visited **at the moment you enqueue/push**, not when you dequeue — otherwise you can enqueue the same node multiple times before processing it.

**BFS (shortest path in unweighted graph):**
```java
boolean[] visited = new boolean[n];
Deque<Integer> queue = new ArrayDeque<>();
queue.offer(start);
visited[start] = true;
while (!queue.isEmpty()) {
    int node = queue.poll();
    // process node
    for (int neighbor : adj.get(node)) {
        if (!visited[neighbor]) {
            visited[neighbor] = true;   // mark on enqueue
            queue.offer(neighbor);
        }
    }
}
```

**DFS recursive:**
```java
void dfs(int node, List<List<Integer>> adj, boolean[] visited) {
    visited[node] = true;
    // process node
    for (int neighbor : adj.get(node)) {
        if (!visited[neighbor]) dfs(neighbor, adj, visited);
    }
}
```

**DFS iterative** (same shape as tree DFS, but needs the visited check because graphs can have cycles):
```java
Deque<Integer> stack = new ArrayDeque<>();
boolean[] visited = new boolean[n];
stack.push(start);
while (!stack.isEmpty()) {
    int node = stack.pop();
    if (visited[node]) continue;
    visited[node] = true;
    // process node
    for (int neighbor : adj.get(node)) {
        if (!visited[neighbor]) stack.push(neighbor);
    }
}
```

**Decision rule**: shortest path / level-by-level → BFS. Exploring all paths, connectivity, cycle detection, backtracking-adjacent problems → DFS.

---

## 6. Dijkstra (shortest path, weighted, non-negative)

Uses PriorityQueue (Phase 2) + adjacency list of `int[]{neighbor, weight}`:
```java
int[] dist = new int[n];
Arrays.fill(dist, Integer.MAX_VALUE);
dist[start] = 0;

PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[1] - b[1]); // {node, distSoFar}
pq.offer(new int[]{start, 0});

while (!pq.isEmpty()) {
    int[] cur = pq.poll();
    int node = cur[0], d = cur[1];
    if (d > dist[node]) continue; // stale entry, skip — this check matters

    for (int[] edge : adj.get(node)) {
        int next = edge[0], weight = edge[1];
        if (dist[node] + weight < dist[next]) {
            dist[next] = dist[node] + weight;
            pq.offer(new int[]{next, dist[next]});
        }
    }
}
```
**Why the `if (d > dist[node]) continue` check**: since we don't remove stale entries from the PQ, a node can get enqueued multiple times with different distances. Skipping outdated ones keeps it correct.

---

## 7. Topological Sort (Kahn's algorithm, BFS-based)

```java
int[] indegree = new int[n];
for (List<Integer> neighbors : adj) for (int v : neighbors) indegree[v]++;

Deque<Integer> queue = new ArrayDeque<>();
for (int i = 0; i < n; i++) if (indegree[i] == 0) queue.offer(i);

List<Integer> order = new ArrayList<>();
while (!queue.isEmpty()) {
    int node = queue.poll();
    order.add(node);
    for (int next : adj.get(node)) {
        if (--indegree[next] == 0) queue.offer(next);
    }
}
// if order.size() < n → cycle exists, no valid topological order
```
**Decision rule**: "order tasks respecting dependencies" / "detect a cycle in a directed graph" → topological sort. `order.size() < n` is your cycle-detection check for free.

---

## 8. Updated "I need to..." → tool table

| I need to... | Use |
|---|---|
| Represent a graph's neighbors | Adjacency list (ArrayList or HashMap of Lists) |
| Shortest path, unweighted | BFS |
| Shortest path, weighted, non-negative | Dijkstra (PriorityQueue) |
| Explore all paths / detect cycles / connectivity | DFS |
| Order tasks by dependency | Topological sort (Kahn's, BFS) |
| Process a tree level by level | BFS with the `levelSize` snapshot trick |
| Get sorted output from a BST | Inorder traversal |
| Reverse a linked list | Three-pointer (`prev`, `cur`, `next`) sweep |
