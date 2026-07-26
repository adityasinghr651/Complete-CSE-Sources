# 📚 Day 15: DFS (Depth-First Search) — Complete Mastery Guide

## SECTION 1: INTUITION BUILDING (Why DFS Exists?)

### 🤔 **Why This Topic Exists?**

**Real-World Problem:**

> Imagine traversing a maze. You pick a path and explore it as deeply as possible until you hit a dead end. Once you hit a dead end, you backtrack to the last fork and try the next available path.
>
> This complete, deep exploration of all choices is the core of **DFS (Depth-First Search)**.

**Mental Model:**

> **DFS = Deep Exploration + Backtracking**
>
> From an interview perspective, DFS isn't just a traversal technique; it's a **problem-solving lens**. When a question demands "all possible paths," "connected regions," "cycles," "subtrees," or "valid orderings", DFS is usually the primary tool.

**Common Interview Use Cases:**
- **Trees**: Preorder, inorder, and postorder traversals are exact variants of DFS.
- **Graphs**: Connected components, cycle detection, path enumeration.
- **Grids**: Island marking, region capturing, flood fill.
- **Backtracking**: Finding all valid combinations or permutations.

---

## SECTION 2: COMPLETE THEORY

### 📖 **Core Concepts & Definitions**

There are two major implementations for DFS traversal: **Recursion** and **Explicit Stack (Iterative)**. Recursive DFS is highly intuitive because the system call stack maintains the path. Iterative DFS is preferred in production to avoid stack overflow on massive depths. For graphs and grids, maintaining a `visited` set is strictly required to prevent infinite loops from cycles.

**Key Concepts:**
- **Preorder**: Process the node before its children.
- **Inorder**: Process the left child, then the node, then the right child.
- **Postorder**: Process the children before the node.
- **Backtracking**: A form of DFS where you make a choice, explore, and then undo that choice (restore state).
- **Cycle Detection**: In directed graphs, this relies on a 3-state coloring scheme: unvisited, visiting (in current path), and visited (fully processed).

**Complexity:**
- **Tree DFS**: Time O(N), Space O(H) where H is the height of the tree.
- **Graph DFS**: Time O(V + E), Space O(V).
- **Grid DFS**: Time O(R × C), Space O(R × C) worst-case.
- **Backtracking DFS**: Worst-case exponential time, depending on the branching factor.

**Tradeoffs vs BFS:**
- DFS is more memory efficient on wide graphs, but deep recursion can overflow.
- BFS is strictly superior for shortest path in unweighted graphs.
- DFS is vastly superior for generating all possibilities, validating structure, and postorder-based problems (like topological sort).

---

## SECTION 3: PATTERN RECOGNITION

### 🔍 **How to Identify DFS Problems?**

Look for these keywords in problem statements: **path, explore, connected, component, region, island, cycle, subtree, all combinations, backtrack, prerequisites**. If a question asks to "try all possible ways" or "find all solutions", DFS + Backtracking is the standard approach.

**When to Use:**
- Graph/Tree traversal.
- Connected component counting (islands, provinces).
- Cycle detection (course schedules).
- Path existence or path enumeration.
- Grid flood fill.
- Backtracking search.

**When NOT to Use:**
- When finding the absolute shortest path in an unweighted graph (use BFS).
- When the graph is exceptionally deep and recursion could blow the stack (use iterative DFS or BFS).
- If single-pass counting with simple math or hashing is possible.

**Similar Patterns:**
- **DFS vs BFS**: Depth-first vs level-first.
- **DFS vs Backtracking**: Backtracking is just DFS + undoing the state.
- **Tree DFS vs Graph DFS**: Graph versions require a `visited` state to handle cycles.
- **Postorder DFS vs Topological Sort**: Inserting nodes in postorder gives dependency ordering.

---

## SECTION 4: VISUAL LEARNING

### **Traversal Orders (Tree)**
```text
Tree:
        1
      /   \
     2     3
    / \
   4   5

Preorder (Node, Left, Right): 1, 2, 4, 5, 3
Inorder (Left, Node, Right): 4, 2, 5, 1, 3
Postorder (Left, Right, Node): 4, 5, 2, 3, 1
```

### **Graph DFS Trace**
```text
Graph:
0 -- 1 -- 3
|    |
2 -- 4

DFS from 0:
0 -> 1 -> 3
backtrack to 1
backtrack to 0 -> 2 -> 4
```

### **Cycle Detection States (Directed Graph)**
```text
0 = unvisited
1 = visiting (currently in the recursion stack)
2 = visited (fully processed)

If during DFS you reach a node with state 1, it means a back edge exists, proving a cycle.
```

### **Backtracking Trace**
```text
Path = []
choose A -> Path = [A]
choose B -> Path = [A, B]
undo B -> Path = [A]
choose C -> Path = [A, C]
undo C -> Path = [A]
undo A -> Path = []
```

---

## SECTION 5: CORE TEMPLATES

### **Template 1: Recursive DFS on Graph**
```java
public void dfs(int node, List<List<Integer>> graph, boolean[] visited) {
    visited[node] = true;
    for (int nei : graph.get(node)) {
        if (!visited[nei]) {
            dfs(nei, graph, visited);
        }
    }
}
```

### **Template 2: Iterative DFS**
```java
public void dfsIterative(int start, List<List<Integer>> graph) {
    boolean[] visited = new boolean[graph.size()];
    Stack<Integer> stack = new Stack<>();
    stack.push(start);

    while (!stack.isEmpty()) {
        int node = stack.pop();
        if (visited[node]) continue;
        visited[node] = true;

        // Push in reverse order to visit in correct order
        for (int i = graph.get(node).size() - 1; i >= 0; i--) {
            int nei = graph.get(node).get(i);
            if (!visited[nei]) stack.push(nei);
        }
    }
}
```

### **Template 3: Cycle Detection in Directed Graph**
```java
public boolean hasCycle(List<List<Integer>> graph) {
    int n = graph.size();
    int[] state = new int[n]; // 0: unvisited, 1: visiting, 2: visited

    for (int i = 0; i < n; i++) {
        if (state[i] == 0 && dfsCycle(i, graph, state)) return true;
    }
    return false;
}

private boolean dfsCycle(int node, List<List<Integer>> graph, int[] state) {
    if (state[node] == 1) return true; // Cycle detected!
    if (state[node] == 2) return false;

    state[node] = 1; // Mark as visiting
    for (int nei : graph.get(node)) {
        if (dfsCycle(nei, graph, state)) return true;
    }
    state[node] = 2; // Fully processed
    return false;
}
```

---

## SECTION 6: PROBLEM PROGRESSION

### **Level 1: Very Easy**

#### **1. Maximum Depth of Binary Tree (LeetCode 104)**
- **Problem**: Return the maximum depth of a binary tree.
- **Approach**: Return `1 + max(leftDepth, rightDepth)`. This is a perfect "return value from children" DFS problem. Time O(N), Space O(H).

**Solution:**
```java
public int maxDepth(TreeNode root) {
    if (root == null) return 0;
    return 1 + Math.max(maxDepth(root.left), maxDepth(root.right));
}
```

#### **2. Binary Tree Preorder Traversal (LeetCode 144)**
- **Problem**: Return the preorder traversal of a tree's nodes' values.
- **Approach**: Process node before exploring children. Time O(N), Space O(H).

**Solution:**
```java
public List<Integer> preorderTraversal(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    dfs(root, result);
    return result;
}

private void dfs(TreeNode root, List<Integer> result) {
    if (root == null) return;
    result.add(root.val);        // Preorder processing
    dfs(root.left, result);
    dfs(root.right, result);
}
```

---

### **Level 2: Easy**

#### **3. Path Sum (LeetCode 112)**
- **Problem**: Check if the tree has a root-to-leaf path summing to a target.
- **Approach**: DFS explores both branches while carrying the remaining sum. Base case at leaf decides success.

**Solution:**
```java
public boolean hasPathSum(TreeNode root, int targetSum) {
    if (root == null) return false;
    
    // If it's a leaf node, check if the remaining sum equals the node's value
    if (root.left == null && root.right == null && targetSum == root.val) {
        return true;
    }
    
    return hasPathSum(root.left, targetSum - root.val) || 
           hasPathSum(root.right, targetSum - root.val);
}
```

#### **4. Flood Fill (LeetCode 733)**
- **Problem**: Recolor connected cells of the same color in a 2D grid.
- **Approach**: Standard Grid DFS. Think of it as painting a region. Time O(R × C).

**Solution:**
```java
public int[][] floodFill(int[][] image, int sr, int sc, int color) {
    if (image[sr][sc] == color) return image;
    dfs(image, sr, sc, image[sr][sc], color);
    return image;
}

private void dfs(int[][] image, int r, int c, int oldColor, int newColor) {
    if (r < 0 || c < 0 || r >= image.length || c >= image[0].length || image[r][c] != oldColor) {
        return;
    }
    
    image[r][c] = newColor; // Paint the cell
    
    dfs(image, r + 1, c, oldColor, newColor);
    dfs(image, r - 1, c, oldColor, newColor);
    dfs(image, r, c + 1, oldColor, newColor);
    dfs(image, r, c - 1, oldColor, newColor);
}
```

#### **5. Number of Islands (LeetCode 200)**
- **Problem**: Count the number of islands (1s) surrounded by water (0s).
- **Approach**: Each DFS marks one island fully. The number of DFS launches equals the number of islands. High-frequency pattern.

**Solution:**
```java
public int numIslands(char[][] grid) {
    int count = 0;
    for (int i = 0; i < grid.length; i++) {
        for (int j = 0; j < grid[0].length; j++) {
            if (grid[i][j] == '1') {
                count++;
                dfs(grid, i, j);
            }
        }
    }
    return count;
}

private void dfs(char[][] grid, int r, int c) {
    if (r < 0 || c < 0 || r >= grid.length || c >= grid[0].length || grid[r][c] == '0') {
        return;
    }
    
    grid[r][c] = '0'; // Sink the island piece
    
    dfs(grid, r + 1, c);
    dfs(grid, r - 1, c);
    dfs(grid, r, c + 1);
    dfs(grid, r, c - 1);
}
```

---

### **Level 3: Medium**

#### **6. Clone Graph (LeetCode 133)**
- **Problem**: Return a deep copy of a graph.
- **Approach**: DFS with a Hash Map to clone nodes while handling cycles. The map prevents infinite recursion.

**Solution:**
```java
public Node cloneGraph(Node node) {
    if (node == null) return null;
    return dfs(node, new HashMap<>());
}

private Node dfs(Node node, Map<Node, Node> visited) {
    if (visited.containsKey(node)) return visited.get(node);
    
    Node clone = new Node(node.val);
    visited.put(node, clone);
    
    for (Node neighbor : node.neighbors) {
        clone.neighbors.add(dfs(neighbor, visited));
    }
    
    return clone;
}
```

#### **7. All Paths From Source to Target (LeetCode 797)**
- **Problem**: Find all paths from node 0 to node n-1.
- **Approach**: Classic DFS backtracking. Maintain current path, recurse, then pop. Learn "choose-explore-unchoose".

**Solution:**
```java
public List<List<Integer>> allPathsSourceTarget(int[][] graph) {
    List<List<Integer>> result = new ArrayList<>();
    List<Integer> path = new ArrayList<>();
    path.add(0);
    dfs(graph, 0, path, result);
    return result;
}

private void dfs(int[][] graph, int node, List<Integer> path, List<List<Integer>> result) {
    if (node == graph.length - 1) {
        result.add(new ArrayList<>(path));
        return;
    }
    
    for (int nei : graph[node]) {
        path.add(nei);                 // Choose
        dfs(graph, nei, path, result); // Explore
        path.remove(path.size() - 1);  // Unchoose (Backtrack)
    }
}
```

#### **8. Word Search (LeetCode 79)**
- **Problem**: Check if a word exists in a grid of characters.
- **Approach**: DFS on grid with backtracking and visited marking (temporarily mutating the board). 

**Solution:**
```java
public boolean exist(char[][] board, String word) {
    for (int i = 0; i < board.length; i++) {
        for (int j = 0; j < board[0].length; j++) {
            if (dfs(board, i, j, word, 0)) return true;
        }
    }
    return false;
}

private boolean dfs(char[][] board, int i, int j, String word, int idx) {
    if (idx == word.length()) return true;
    if (i < 0 || j < 0 || i >= board.length || j >= board[0].length || board[i][j] != word.charAt(idx)) {
        return false;
    }
    
    char temp = board[i][j];
    board[i][j] = '#'; // Mark visited
    
    boolean found = dfs(board, i + 1, j, word, idx + 1) ||
                    dfs(board, i - 1, j, word, idx + 1) ||
                    dfs(board, i, j + 1, word, idx + 1) ||
                    dfs(board, i, j - 1, word, idx + 1);
                    
    board[i][j] = temp; // Backtrack
    return found;
}
```

#### **9. Course Schedule (LeetCode 207)**
- **Problem**: Determine if you can finish all courses given prerequisite edges.
- **Approach**: DFS 3-state cycle detection. If a back edge is detected, completion is impossible.

**Solution:**
```java
public boolean canFinish(int numCourses, int[][] prerequisites) {
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i < numCourses; i++) adj.add(new ArrayList<>());
    for (int[] p : prerequisites) adj.get(p[1]).add(p[0]);
    
    int[] state = new int[numCourses];
    for (int i = 0; i < numCourses; i++) {
        if (state[i] == 0 && dfs(i, adj, state)) return false; // Cycle found
    }
    return true;
}

private boolean dfs(int node, List<List<Integer>> adj, int[] state) {
    if (state[node] == 1) return true; // Back edge
    if (state[node] == 2) return false;
    
    state[node] = 1;
    for (int nei : adj.get(node)) {
        if (dfs(nei, adj, state)) return true;
    }
    state[node] = 2;
    
    return false;
}
```

---

### **Level 4: Hard**

#### **10. Critical Connections in a Network (LeetCode 1192)**
- **Problem**: Find all critical connections (bridges) in an undirected network.
- **Approach**: Tarjan's Bridge-Finding Algorithm. Use discovery times and low-link values during DFS. Top-tier FAANG graph question.

**Solution:**
```java
int timer = 0;

public List<List<Integer>> criticalConnections(int n, List<List<Integer>> connections) {
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
    for (List<Integer> edge : connections) {
        adj.get(edge.get(0)).add(edge.get(1));
        adj.get(edge.get(1)).add(edge.get(0));
    }
    
    int[] disc = new int[n];
    int[] low = new int[n];
    List<List<Integer>> res = new ArrayList<>();
    
    dfs(0, -1, adj, disc, low, res);
    return res;
}

private void dfs(int node, int parent, List<List<Integer>> adj, int[] disc, int[] low, List<List<Integer>> res) {
    disc[node] = low[node] = ++timer;
    
    for (int nei : adj.get(node)) {
        if (nei == parent) continue;
        
        if (disc[nei] == 0) { // Unvisited
            dfs(nei, node, adj, disc, low, res);
            low[node] = Math.min(low[node], low[nei]);
            
            if (low[nei] > disc[node]) { // Bridge condition
                res.add(Arrays.asList(node, nei));
            }
        } else {
            low[node] = Math.min(low[node], disc[nei]); // Back edge
        }
    }
}
```

#### **11. Word Search II (LeetCode 212)**
- **Problem**: Find all valid words from a dictionary in a grid.
- **Approach**: Trie + DFS + Backtracking. Combine prefix pruning with grid search. Masterpiece of pattern composition.

**Solution:**
```java
class TrieNode {
    TrieNode[] children = new TrieNode[26];
    String word = null;
}

public List<String> findWords(char[][] board, String[] words) {
    TrieNode root = new TrieNode();
    for (String word : words) {
        TrieNode curr = root;
        for (char c : word.toCharArray()) {
            if (curr.children[c - 'a'] == null) curr.children[c - 'a'] = new TrieNode();
            curr = curr.children[c - 'a'];
        }
        curr.word = word;
    }
    
    List<String> res = new ArrayList<>();
    for (int i = 0; i < board.length; i++) {
        for (int j = 0; j < board[0].length; j++) {
            dfs(board, i, j, root, res);
        }
    }
    return res;
}

private void dfs(char[][] board, int i, int j, TrieNode node, List<String> res) {
    if (i < 0 || j < 0 || i >= board.length || j >= board[0].length || board[i][j] == '#') return;
    
    char c = board[i][j];
    if (node.children[c - 'a'] == null) return; // Prune
    
    TrieNode nextNode = node.children[c - 'a'];
    if (nextNode.word != null) { // Word found
        res.add(nextNode.word);
        nextNode.word = null; // Deduplicate
    }
    
    board[i][j] = '#';
    dfs(board, i + 1, j, nextNode, res);
    dfs(board, i - 1, j, nextNode, res);
    dfs(board, i, j + 1, nextNode, res);
    dfs(board, i, j - 1, nextNode, res);
    board[i][j] = c;
}
```

---

### **Level 5: FAANG Level**

#### **12. Pacific Atlantic Water Flow (LeetCode 417)**
- **Problem**: Find coordinates where water can flow to both Pacific and Atlantic oceans.
- **Approach**: Reverse DFS from oceans inward. Instead of asking where water can go, ask from which cells the ocean can be reached.

**Solution:**
```java
public List<List<Integer>> pacificAtlantic(int[][] heights) {
    int r = heights.length, c = heights[0].length;
    boolean[][] pac = new boolean[r][c], atl = new boolean[r][c];
    
    for (int i = 0; i < r; i++) {
        dfs(heights, pac, i, 0);
        dfs(heights, atl, i, c - 1);
    }
    for (int j = 0; j < c; j++) {
        dfs(heights, pac, 0, j);
        dfs(heights, atl, r - 1, j);
    }
    
    List<List<Integer>> res = new ArrayList<>();
    for (int i = 0; i < r; i++) {
        for (int j = 0; j < c; j++) {
            if (pac[i][j] && atl[i][j]) res.add(Arrays.asList(i, j));
        }
    }
    return res;
}

private void dfs(int[][] heights, boolean[][] visited, int i, int j) {
    visited[i][j] = true;
    int[][] dirs = {{0,1}, {1,0}, {0,-1}, {-1,0}};
    
    for (int[] dir : dirs) {
        int ni = i + dir[0], nj = j + dir[1];
        // Water flows from higher to lower, so we reverse it (lower to higher)
        if (ni >= 0 && ni < heights.length && nj >= 0 && nj < heights[0].length 
            && !visited[ni][nj] && heights[ni][nj] >= heights[i][j]) {
            dfs(heights, visited, ni, nj);
        }
    }
}
```

#### **13. Binary Tree Maximum Path Sum (LeetCode 124)**
- **Problem**: Find the maximum path sum between any two nodes.
- **Approach**: DFS returns the best downward gain from each node while updating the global answer for paths passing through the node. This is "tree DP disguised as DFS".

**Solution:**
```java
int max = Integer.MIN_VALUE;

public int maxPathSum(TreeNode root) {
    dfs(root);
    return max;
}

private int dfs(TreeNode root) {
    if (root == null) return 0;
    
    // Ignore negative branches
    int left = Math.max(0, dfs(root.left));
    int right = Math.max(0, dfs(root.right));
    
    // Update global max with path spanning across root
    max = Math.max(max, root.val + left + right);
    
    // Return max path sum going down one side
    return root.val + Math.max(left, right);
}
```

---

## SECTION 7: COMPANY QUESTIONS

- **Google**: Word Search, Pacific Atlantic, Course Schedule, Maximum Path Sum.
- **Meta**: Number of Islands, Clone Graph, All Paths From Source to Target.
- **Amazon**: Path Sum, Word Search II, Flood Fill.
- **Apple**: Traversal-order questions, tree property validation, recursion depth.
- **Netflix**: Tree DFS, graph traversal, structural checks.
- **Microsoft**: Clone Graph, Word Search, DFS on grids.
- **Uber**: Path search, state-space DFS, graph routing.
- **Airbnb**: Islands, connectivity, region capture.
- **LinkedIn**: Graph connectivity and social-network style DFS.
- **Atlassian**: Dependency cycles, order validation.
- **Databricks**: Large-scale graph traversal and component analysis.

---

## SECTION 8: LeetCode Top 20 Must-Solve

| Category | Problem | Importance | Frequency |
|---|---|---:|---:|
| Easy | Preorder Traversal | 9/10 | 10/10 |
| Easy | Maximum Depth of Binary Tree | 10/10 | 10/10 |
| Easy | Path Sum | 9/10 | 9/10 |
| Easy | Flood Fill | 8/10 | 8/10 |
| Easy | Number of Islands | 10/10 | 10/10 |
| Medium | Clone Graph | 10/10 | 10/10 |
| Medium | All Paths From Source to Target | 9/10 | 9/10 |
| Medium | Word Search | 10/10 | 10/10 |
| Medium | Course Schedule | 10/10 | 10/10 |
| Medium | Path Sum II | 9/10 | 9/10 |
| Medium | Binary Tree Paths | 8/10 | 9/10 |
| Medium | Pacific Atlantic Water Flow | 9/10 | 9/10 |
| Medium | Number of Enclaves | 8/10 | 8/10 |
| Medium | Surrounded Regions | 9/10 | 9/10 |
| Hard | Word Search II | 10/10 | 10/10 |
| Hard | Critical Connections | 9/10 | 8/10 |
| Hard | Binary Tree Maximum Path Sum | 10/10 | 10/10 |
| Hard | Reconstruct Itinerary | 10/10 | 9/10 |
| Hard | Deserialize/Serialize Tree | 10/10 | 10/10 |
| Hard | Longest Increasing Path in a Matrix | 9/10 | 9/10 |

---

## SECTION 9: Codeforces Mastery

### **Beginner**
- **Rating 800**: Tree recursion, simple DFS traversal.
- **Rating 1000**: Flood fill / connected region counting.
- **Rating 1200**: Path exploration in grid or tree.

### **Intermediate**
- **Rating 1400**: Directed cycle detection.
- **Rating 1600**: Backtracking with pruning or graph bridge ideas.

**Why these are chosen:**
These problems build DFS intuition in the same order interviews do: first traversal, then components, then cycles, then advanced state and pruning. This gives you strong contest instincts instead of memorized templates.

---

## SECTION 10: Contest Training

DFS in contests is about recognizing structure fast. If you see a grid and need to mark a region, think DFS flood fill. If you need all solutions or all valid paths, think DFS with backtracking. If you need to validate prerequisites or detect loops, think DFS coloring.

### **Pressure Strategy**
- Draw the state tree first.
- Identify whether you need visitation, path recording, or state restoration.
- Decide recursive vs iterative.
- If recursion depth may explode, move to stack.

### **Common Mistakes**
- Forgetting `visited`.
- Forgetting to restore board/path in backtracking.
- Using DFS when shortest path is required.
- Mixing tree assumptions with graph cycles.
- Returning too early and missing valid branches.

---

## SECTION 11: Active Learning

Solve these 5 and refine your implementation:

1. [Number of Islands](https://leetcode.com/problems/number-of-islands/)
2. [Clone Graph](https://leetcode.com/problems/clone-graph/)
3. [Word Search](https://leetcode.com/problems/word-search/)
4. [All Paths From Source to Target](https://leetcode.com/problems/all-paths-from-source-to-target/)
5. [Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow/)

---

## SECTION 12: Interview Simulation

**FAANG Interviewer Mode Activated! 🎯**

**Interviewer:** "How would you solve Word Search?"

**Candidate (You):**
A strong answer should include:
- DFS from each cell.
- Mark visited cells temporarily.
- Recurse in 4 directions.
- Restore the cell after exploration (backtracking).
- Stop early when the word is fully matched.
- Complexity analysis with worst-case exponential behavior.

**Follow-ups to Defend:**
- What if you need all matching words? (Lead into Trie structure)
- What if the board is very large? (Lead into space constraints and iterative stack)
- Can you reduce repeated work?

---

## SECTION 13: Revision Notes — CHEAT SHEET

### **One-page Sheet**
- **DFS** = Explore deep, then backtrack.
- Recursive DFS uses call stack.
- Graph DFS always needs `visited`.
- Tree DFS supports preorder/inorder/postorder.
- **Backtracking** = DFS + undo state.
- **3-state coloring** detects directed cycles.
- **Reverse DFS** is useful when direction seems awkward (e.g. starting from destination).
- **Bridges/Critical connections** need DFS timestamps (Tarjan's).

### **Interview Checklist**
- Is this a graph/tree/grid?
- Is the goal traversal, path, component, or cycle?
- Do I need all answers? (Backtracking)
- Do I need to restore state? (Backtracking)
- Is recursion depth safe? (Stack overflow check)
- Is there a reverse-thinking trick?

---

## SECTION 14: Final Mastery Test

### **Easy**
1. [Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/) — LeetCode 104
2. [Number of Islands](https://leetcode.com/problems/number-of-islands/) — LeetCode 200

### **Medium**
1. [Clone Graph](https://leetcode.com/problems/clone-graph/) — LeetCode 133
2. [Word Search](https://leetcode.com/problems/word-search/) — LeetCode 79
3. [Course Schedule](https://leetcode.com/problems/course-schedule/) — LeetCode 207

### **Hard**
1. [Critical Connections in a Network](https://leetcode.com/problems/critical-connections-in-a-network/) — LeetCode 1192
2. [Word Search II](https://leetcode.com/problems/word-search-ii/) — LeetCode 212

**Contest-Style:**
- Complete one grid DFS, one graph DFS, and one tree DFS in 60–90 minutes.

**Interview-Style:**
- Explain DFS on trees and graphs.
- Explain recursive vs iterative DFS.
- Explain DFS cycle detection.
- Explain one backtracking example with state restoration.
