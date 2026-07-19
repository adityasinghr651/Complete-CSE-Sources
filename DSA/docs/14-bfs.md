# 📚 Day 14: BFS (Breadth-First Search) — Complete Mastery Guide

## SECTION 1: INTUITION BUILDING (Why BFS Exists?)

### 🤔 **Why This Topic Exists?**

**Real-World Problem:**

> Imagine you want the **shortest route** in Google Maps from city A to city B (assuming all roads are equal distance). Or you want to find the **minimum mutual friends** path between you and a stranger on a social network.
>
> These problems are solved using **BFS**!

**Real-World Analogies:**

| Real World | BFS Equivalent |
|------------|----------------|
| RIP (Rest in Peace) in Games | Level-by-level exploration |
| Social Network Suggestions | Friends of friends (2nd degree connections) |
| Website Crawling | Google bot explores links level by level |
| Emergency Broadcast | Alert spreads to neighbors first, then their neighbors |
| Multiplayer Game Matchmaking | Find closest players first |
| Maze Solving | Shortest path from start to exit |

**Visual Example:**

```text
Social Network - Find shortest path from You to Stranger:

        You
       /   \
    Alice   Bob
    /  \    / \
  Carol Dave Eve  Frank
               \
              Stranger

BFS explores level by level:
Level 0: You
Level 1: Alice, Bob
Level 2: Carol, Dave, Eve, Frank
Level 3: Stranger ✓

Shortest path: You → Bob → Eve → Stranger (3 steps)
```

**Mental Model:**

> **BFS = Level-by-Level Exploration**
>
> Whenever you need the **shortest path** in an **unweighted graph**, or need to perform a **level order** traversal → **THINK BFS!**

**Common Interview Use Cases:**
- **Shortest Path**: Unweighted graphs, mazes, grids
- **Level Order Traversal**: Trees, N-ary trees
- **Connected Components**: Number of islands, provinces
- **Minimum Steps**: Word ladder, transformations
- **Web Crawling**: Search engines, link analysis
- **Network Broadcasting**: Spread information efficiently

---

## SECTION 2: COMPLETE THEORY

### 📖 **Core Concepts & Definitions**

#### **Definition:**

> **Breadth-First Search (BFS)** is a **graph traversal algorithm** that explores all nodes at the **current depth level** before moving to nodes at the **next depth level**.
>
> It uses a **Queue (FIFO)** data structure to maintain the order of exploration.

**Key Properties:**

| Property | Description | Why Important |
|----------|-------------|---------------|
| **Queue-based** | FIFO order ensures level-by-level | Shortest path guarantee |
| **Visited Array** | Prevents revisiting nodes | Avoids infinite loops |
| **Distance Tracking** | Can track steps from source | Minimum distance calculation |
| **Completeness** | Always finds solution if exists | Reliable for search problems |
| **Optimality** | Finds shortest path in unweighted graphs | Guaranteed minimum steps |

**Visual Diagram:**

```text
Graph:
        0
       / \
      1   2
     / \   \
    3   4   5

BFS Traversal from node 0:

Step 1: Queue = [0], Visited = {0}
        Process: 0
        Add neighbors: 1, 2
        
Step 2: Queue = [1, 2], Visited = {0, 1, 2}
        Process: 1
        Add neighbors: 3, 4
        
Step 3: Queue = [2, 3, 4], Visited = {0, 1, 2, 3, 4}
        Process: 2
        Add neighbors: 5
        
Step 4: Queue = [3, 4, 5], Visited = {0, 1, 2, 3, 4, 5}
        Process: 3
        Add neighbors: (none)
        
Step 5: Queue = [4, 5], Visited = {0, 1, 2, 3, 4, 5}
        Process: 4
        Add neighbors: (none)
        
Step 6: Queue = [5], Visited = {0, 1, 2, 3, 4, 5}
        Process: 5
        Add neighbors: (none)
        
Final Order: 0, 1, 2, 3, 4, 5 ✓

Level Structure:
Level 0: [0]
Level 1: [1, 2]
Level 2: [3, 4, 5]
```

---

### **BFS Algorithm Steps:**

```text
Algorithm BFS(Graph, startNode):
    1. Initialize:
       - Queue Q
       - Visited array/set
      
    2. Mark startNode as visited
    3. Enqueue startNode to Q
    
    4. While Q is not empty:
       a. Dequeue current node
       b. Process current node
       c. For each neighbor of current node:
          - If neighbor not visited:
            * Mark neighbor as visited
            * Enqueue neighbor to Q
    
    5. Return result
```

**Time Complexity:** O(V + E)
- Every vertex visited once: O(V)
- Every edge traversed once: O(E)

**Space Complexity:** O(V)
- Queue can hold up to V nodes
- Visited array: O(V)

---

### **BFS vs DFS Comparison:**

| Aspect | BFS | DFS |
|--------|-----|-----|
| **Data Structure** | Queue (FIFO) | Stack (LIFO) or Recursion |
| **Traversal Order** | Level-by-level | Depth-first (go deep) |
| **Shortest Path** | ✓ Guaranteed (unweighted) | ✗ Not guaranteed |
| **Space** | O(V) (can be large) | O(h) where h = height |
| **Use Case** | Shortest path, levels | Path finding, cycles |
| **Implementation** | Iterative | Recursive or Iterative |

---

### **BFS Variations:**

| Variation | Description | Use Case |
|-----------|-------------|----------|
| **Standard BFS** | Single source traversal | Connectivity, reachability |
| **Level Order BFS** | Track levels explicitly | Tree level order, distance |
| **Multi-Source BFS** | Start from multiple sources | Nearest distance from any source |
| **BFS with Path** | Track actual path | Reconstruct shortest path |
| **BFS on Grid** | 2D matrix traversal | Maze, island problems |
| **Bidirectional BFS** | Search from both ends | Shortest path optimization |

---

## SECTION 3: PATTERN RECOGNITION

### 🔍 **How to Identify BFS Problems?**

**Keywords in Interview Questions:**

| Keyword | Indicates | Example |
|---------|-----------|---------|
| **Shortest Path** | BFS (unweighted) | Minimum steps, shortest distance |
| **Minimum** | BFS optimization | Minimum transformations, minimum swaps |
| **Level** | Level-by-level | Level order traversal, depth |
| **Nearest** | BFS from source | Nearest exit, closest node |
| **Steps** | BFS distance | Minimum steps to reach target |
| **Transform** | BFS states | Word ladder, state transitions |
| **Spread** | BFS propagation | Virus spread, information flow |
| **Distance** | BFS layers | K-distance nodes, radius |

**When to Use BFS:**

✅ **Use BFS when:**
- Finding shortest path in unweighted graph
- Need minimum number of steps
- Level-by-level traversal required
- Multiple sources to nearest target
- State space search (puzzles, transformations)

❌ **Don't Use BFS when:**
- Graph is weighted (use Dijkstra)
- Need to explore all paths (use DFS)
- Memory is very limited (DFS uses less)
- Need to detect cycles (DFS is better)

---

### **Common BFS Patterns:**

| Pattern | When to Use | Example Problems |
|---------|-------------|------------------|
| **Standard BFS** | Simple traversal, connectivity | Number of islands, graph traversal |
| **Level Order BFS** | Track distance/levels | Binary tree level order, shortest path |
| **Multi-Source BFS** | Nearest from multiple sources | 01 Matrix, nearest exit |
| **BFS with State** | Track additional info | Word ladder, transformations |
| **BFS on Grid** | 2D matrix problems | Shortest path in binary matrix |
| **Bidirectional BFS** | Optimize shortest path | Word ladder II optimization |

---

## SECTION 4: VISUAL LEARNING

### **BFS Step-by-Step Trace:**

```text
Graph:
        0
       / \
      1   2
     / \   \
    3   4   5

BFS from node 0:

Initial State:
Queue: [0]
Visited: {0}
Result: []

Step 1:
Dequeue: 0
Queue: []
Visited: {0}
Result: [0]
Add neighbors of 0: 1, 2
Queue: [1, 2]
Visited: {0, 1, 2}

Step 2:
Dequeue: 1
Queue: [2]
Visited: {0, 1, 2}
Result: [0, 1]
Add neighbors of 1: 3, 4
Queue: [2, 3, 4]
Visited: {0, 1, 2, 3, 4}

Step 3:
Dequeue: 2
Queue: [3, 4]
Visited: {0, 1, 2, 3, 4}
Result: [0, 1, 2]
Add neighbors of 2: 5
Queue: [3, 4, 5]
Visited: {0, 1, 2, 3, 4, 5}

Step 4:
Dequeue: 3
Queue: [4, 5]
Visited: {0, 1, 2, 3, 4, 5}
Result: [0, 1, 2, 3]
Add neighbors of 3: (none)
Queue: [4, 5]

Step 5:
Dequeue: 4
Queue: [5]
Visited: {0, 1, 2, 3, 4, 5}
Result: [0, 1, 2, 3, 4]
Add neighbors of 4: (none)
Queue: [5]

Step 6:
Dequeue: 5
Queue: []
Visited: {0, 1, 2, 3, 4, 5}
Result: [0, 1, 2, 3, 4, 5]
Add neighbors of 5: (none)
Queue: []

Final BFS Order: 0, 1, 2, 3, 4, 5 ✓

Level Structure:
Level 0: 0
Level 1: 1, 2
Level 2: 3, 4, 5
```

---

### **BFS on Grid Visualization:**

```text
Grid (0 = empty, 1 = obstacle):
0 0 0
0 1 0
0 0 0

Find shortest path from (0,0) to (2,2):

BFS explores:

Step 0: Queue = [(0,0)]
        Distance = 0
        (0,0) ✓

Step 1: Queue = [(0,1), (1,0)]
        Distance = 1
        (0,0) → (0,1), (1,0) ✓

Step 2: Queue = [(1,0), (0,2)]
        Distance = 2
        (0,1) → (0,2) ✓
        (1,0) can't go to (1,1) (obstacle)

Step 3: Queue = [(0,2), (2,0)]
        Distance = 2
        (1,0) → (2,0) ✓

Step 4: Queue = [(2,0), (1,2)]
        Distance = 3
        (0,2) → (1,2) ✓

Step 5: Queue = [(1,2), (2,1)]
        Distance = 3
        (2,0) → (2,1) ✓

Step 6: Queue = [(2,1), (2,2)]
        Distance = 4
        (1,2) → (2,2) ✓ TARGET REACHED!

Shortest Path: (0,0) → (0,1) → (0,2) → (1,2) → (2,2)
Distance: 4 steps ✓
```

---

## SECTION 5: CORE TEMPLATES

### **Template 1: Standard BFS (Graph)**

**Java:**
```java
public List<Integer> bfs(Graph g, int start) {
    List<Integer> result = new ArrayList<>();
    boolean[] visited = new boolean[g.V];
    Queue<Integer> queue = new LinkedList<>();
    
    // Mark start as visited and enqueue
    visited[start] = true;
    queue.offer(start);
    
    while (!queue.isEmpty()) {
        int curr = queue.poll();
        result.add(curr);
        
        // Visit all neighbors
        for (int neighbor : g.adjList.get(curr)) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                queue.offer(neighbor);
            }
        }
    }
    
    return result;
}

// Time: O(V + E) | Space: O(V)
```

**C++:**
```cpp
vector<int> bfs(Graph& g, int start) {
    vector<int> result;
    vector<bool> visited(g.V, false);
    queue<int> q;
    
    visited[start] = true;
    q.push(start);
    
    while (!q.empty()) {
        int curr = q.front();
        q.pop();
        result.push_back(curr);
        
        for (int neighbor : g.adjList[curr]) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                q.push(neighbor);
            }
        }
    }
    
    return result;
}
```

---

### **Template 2: BFS with Level Tracking**

**Java:**
```java
public List<List<Integer>> bfsLevelOrder(Graph g, int start) {
    List<List<Integer>> result = new ArrayList<>();
    boolean[] visited = new boolean[g.V];
    Queue<Integer> queue = new LinkedList<>();
    
    visited[start] = true;
    queue.offer(start);
    
    while (!queue.isEmpty()) {
        int size = queue.size();
        List<Integer> level = new ArrayList<>();
        
        // Process all nodes at current level
        for (int i = 0; i < size; i++) {
            int curr = queue.poll();
            level.add(curr);
            
            for (int neighbor : g.adjList.get(curr)) {
                if (!visited[neighbor]) {
                    visited[neighbor] = true;
                    queue.offer(neighbor);
                }
            }
        }
        
        result.add(level);
    }
    
    return result;
}

// Time: O(V + E) | Space: O(V)
```

---

### **Template 3: BFS with Distance Tracking**

**Java:**
```java
public int bfsShortestPath(Graph g, int start, int target) {
    boolean[] visited = new boolean[g.V];
    Queue<Integer> queue = new LinkedList<>();
    int[] distance = new int[g.V];
    
    visited[start] = true;
    distance[start] = 0;
    queue.offer(start);
    
    while (!queue.isEmpty()) {
        int curr = queue.poll();
        
        if (curr == target) {
            return distance[curr];
        }
        
        for (int neighbor : g.adjList.get(curr)) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                distance[neighbor] = distance[curr] + 1;
                queue.offer(neighbor);
            }
        }
    }
    
    return -1;  // Target not reachable
}

// Time: O(V + E) | Space: O(V)
```

---

### **Template 4: BFS on Grid (2D Matrix)**

**Java:**
```java
public int bfsGrid(int[][] grid, int[] start, int[] target) {
    int rows = grid.length;
    int cols = grid[0].length;
    boolean[][] visited = new boolean[rows][cols];
    Queue<int[]> queue = new LinkedList<>();
    
    int[][] directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};  // Up, Down, Left, Right
    
    queue.offer(start);
    visited[start[0]][start[1]] = true;
    int steps = 0;
    
    while (!queue.isEmpty()) {
        int size = queue.size();
        
        for (int i = 0; i < size; i++) {
            int[] curr = queue.poll();
            int row = curr[0];
            int col = curr[1];
            
            if (row == target[0] && col == target[1]) {
                return steps;
            }
            
            // Explore all 4 directions
            for (int[] dir : directions) {
                int newRow = row + dir[0];
                int newCol = col + dir[1];
                
                // Check bounds and validity
                if (newRow >= 0 && newRow < rows && 
                    newCol >= 0 && newCol < cols &&
                    !visited[newRow][newCol] &&
                    grid[newRow][newCol] == 0) {  // 0 = valid cell
                    
                    visited[newRow][newCol] = true;
                    queue.offer(new int[]{newRow, newCol});
                }
            }
        }
        
        steps++;
    }
    
    return -1;  // Target not reachable
}

// Time: O(rows × cols) | Space: O(rows × cols)
```

---

### **Template 5: Multi-Source BFS**

**Java:**
```java
public int[][] multiSourceBFS(int[][] grid) {
    int rows = grid.length;
    int cols = grid[0].length;
    int[][] distance = new int[rows][cols];
    Queue<int[]> queue = new LinkedList<>();
    
    // Initialize: add all sources (0s) to queue
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (grid[i][j] == 0) {
                queue.offer(new int[]{i, j});
                distance[i][j] = 0;
            } else {
                distance[i][j] = Integer.MAX_VALUE;
            }
        }
    }
    
    int[][] directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
    
    while (!queue.isEmpty()) {
        int[] curr = queue.poll();
        int row = curr[0];
        int col = curr[1];
        
        for (int[] dir : directions) {
            int newRow = row + dir[0];
            int newCol = col + dir[1];
            
            if (newRow >= 0 && newRow < rows && 
                newCol >= 0 && newCol < cols &&
                distance[newRow][newCol] > distance[row][col] + 1) {
                
                distance[newRow][newCol] = distance[row][col] + 1;
                queue.offer(new int[]{newRow, newCol});
            }
        }
    }
    
    return distance;
}

// Time: O(rows × cols) | Space: O(rows × cols)
```

---

## SECTION 6: PROBLEM PROGRESSION

### **Level 1: Very Easy**

#### **1. Number of Provinces (LeetCode 547)**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/number-of-provinces/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 9/10
- **Problem**: Find number of connected components using BFS

**Solution (BFS):**
```java
public int findCircleNum(int[][] isConnected) {
    int V = isConnected.length;
    boolean[] visited = new boolean[V];
    int provinces = 0;
    
    for (int i = 0; i < V; i++) {
        if (!visited[i]) {
            bfs(isConnected, visited, i);
            provinces++;
        }
    }
    
    return provinces;
}

private void bfs(int[][] isConnected, boolean[] visited, int start) {
    Queue<Integer> queue = new LinkedList<>();
    queue.offer(start);
    visited[start] = true;
    
    while (!queue.isEmpty()) {
        int curr = queue.poll();
        
        for (int neighbor = 0; neighbor < isConnected.length; neighbor++) {
            if (isConnected[curr][neighbor] == 1 && !visited[neighbor]) {
                visited[neighbor] = true;
                queue.offer(neighbor);
            }
        }
    }
}

// Time: O(V²) | Space: O(V)
```

---

#### **2. Find if Path Exists in Graph (LeetCode 1971)**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/find-if-path-exists-in-graph/
- **Company Tags**: Amazon, Microsoft
- **Frequency**: 8/10

**Solution (BFS):**
```java
public boolean validPath(int n, int[][] edges, int source, int destination) {
    List<List<Integer>> adjList = new ArrayList<>();
    for (int i = 0; i < n; i++) {
        adjList.add(new ArrayList<>());
    }
    
    for (int[] edge : edges) {
        adjList.get(edge[0]).add(edge[1]);
        adjList.get(edge[1]).add(edge[0]);
    }
    
    boolean[] visited = new boolean[n];
    Queue<Integer> queue = new LinkedList<>();
    queue.offer(source);
    visited[source] = true;
    
    while (!queue.isEmpty()) {
        int curr = queue.poll();
        if (curr == destination) return true;
        
        for (int neighbor : adjList.get(curr)) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                queue.offer(neighbor);
            }
        }
    }
    
    return false;
}

// Time: O(V + E) | Space: O(V)
```

---

### **Level 2: Easy**

#### **3. Keys and Rooms (LeetCode 841)**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/keys-and-rooms/
- **Company Tags**: Google, Meta, Amazon
- **Frequency**: 8/10
- **Problem**: Can you visit all rooms starting from room 0?

**Solution (BFS):**
```java
public boolean canVisitAllRooms(List<List<Integer>> rooms) {
    int n = rooms.size();
    boolean[] visited = new boolean[n];
    Queue<Integer> queue = new LinkedList<>();
    
    queue.offer(0);
    visited[0] = true;
    int visitedCount = 1;
    
    while (!queue.isEmpty()) {
        int curr = queue.poll();
        
        for (int key : rooms.get(curr)) {
            if (!visited[key]) {
                visited[key] = true;
                queue.offer(key);
                visitedCount++;
            }
        }
    }
    
    return visitedCount == n;
}

// Time: O(V + E) | Space: O(V)
```

---

#### **4. Find Center of Star Graph (LeetCode 1791)**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/find-center-of-star-graph/
- **Company Tags**: Amazon, Microsoft
- **Frequency**: 7/10

**Solution (BFS approach):**
```java
public int findCenter(int[][] edges) {
    // Center appears in every edge
    // Check first two edges
    if (edges[0][0] == edges[1][0] || edges[0][0] == edges[1][1]) {
        return edges[0][0];
    }
    return edges[0][1];
}

// Time: O(1) | Space: O(1)
```

---

### **Level 3: Medium**

#### **5. Shortest Path in Binary Matrix (LeetCode 1091)**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/shortest-path-in-binary-matrix/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 9/10
- **Problem**: Find shortest path from (0,0) to (n-1,n-1) in binary matrix

**Solution (BFS on Grid):**
```java
public int shortestPathBinaryMatrix(int[][] grid) {
    int n = grid.length;
    
    if (grid[0][0] == 1 || grid[n-1][n-1] == 1) {
        return -1;
    }
    
    int[][] directions = {
        {-1, -1}, {-1, 0}, {-1, 1},
        {0, -1},           {0, 1},
        {1, -1},  {1, 0},  {1, 1}
    };
    
    Queue<int[]> queue = new LinkedList<>();
    queue.offer(new int[]{0, 0, 1});  // {row, col, distance}
    grid[0][0] = 1;  // Mark as visited
    
    while (!queue.isEmpty()) {
        int[] curr = queue.poll();
        int row = curr[0];
        int col = curr[1];
        int dist = curr[2];
        
        if (row == n - 1 && col == n - 1) {
            return dist;
        }
        
        for (int[] dir : directions) {
            int newRow = row + dir[0];
            int newCol = col + dir[1];
            
            if (newRow >= 0 && newRow < n && 
                newCol >= 0 && newCol < n &&
                grid[newRow][newCol] == 0) {
                
                grid[newRow][newCol] = 1;  // Mark as visited
                queue.offer(new int[]{newRow, newCol, dist + 1});
            }
        }
    }
    
    return -1;
}

// Time: O(n²) | Space: O(n²)
```

---

#### **6. 01 Matrix (LeetCode 542)**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/01-matrix/
- **Company Tags**: Google, Meta, Amazon
- **Frequency**: 9/10
- **Problem**: Distance of nearest 0 for each cell

**Solution (Multi-Source BFS):**
```java
public int[][] updateMatrix(int[][] mat) {
    int rows = mat.length;
    int cols = mat[0].length;
    int[][] distance = new int[rows][cols];
    Queue<int[]> queue = new LinkedList<>();
    
    // Initialize: add all 0s to queue
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (mat[i][j] == 0) {
                queue.offer(new int[]{i, j});
                distance[i][j] = 0;
            } else {
                distance[i][j] = Integer.MAX_VALUE;
            }
        }
    }
    
    int[][] directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
    
    while (!queue.isEmpty()) {
        int[] curr = queue.poll();
        int row = curr[0];
        int col = curr[1];
        
        for (int[] dir : directions) {
            int newRow = row + dir[0];
            int newCol = col + dir[1];
            
            if (newRow >= 0 && newRow < rows && 
                newCol >= 0 && newCol < cols &&
                distance[newRow][newCol] > distance[row][col] + 1) {
                
                distance[newRow][newCol] = distance[row][col] + 1;
                queue.offer(new int[]{newRow, newCol});
            }
        }
    }
    
    return distance;
}

// Time: O(rows × cols) | Space: O(rows × cols)
```

---

#### **7. Word Ladder (LeetCode 127)**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/word-ladder/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 10/10
- **Problem**: Transform beginWord to endWord with minimum steps

**Solution (BFS with State):**
```java
public int ladderLength(String beginWord, String endWord, List<String> wordList) {
    Set<String> wordSet = new HashSet<>(wordList);
    if (!wordSet.contains(endWord)) return 0;
    
    Queue<String> queue = new LinkedList<>();
    queue.offer(beginWord);
    wordSet.remove(beginWord);
    int steps = 1;
    
    while (!queue.isEmpty()) {
        int size = queue.size();
        
        for (int i = 0; i < size; i++) {
            String curr = queue.poll();
            
            if (curr.equals(endWord)) {
                return steps;
            }
            
            // Try changing each character
            char[] chars = curr.toCharArray();
            for (int j = 0; j < chars.length; j++) {
                char original = chars[j];
                
                for (char c = 'a'; c <= 'z'; c++) {
                    chars[j] = c;
                    String newWord = new String(chars);
                    
                    if (wordSet.contains(newWord)) {
                        queue.offer(newWord);
                        wordSet.remove(newWord);
                    }
                }
                
                chars[j] = original;
            }
        }
        
        steps++;
    }
    
    return 0;
}

// Time: O(N × M² × 26) | Space: O(N × M)
// N = number of words, M = length of each word
```

---

### **Level 4: Hard**

#### **8. Word Ladder II (LeetCode 126)**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/word-ladder-ii/
- **Company Tags**: Google, Meta, Amazon
- **Frequency**: 9/10
- **Problem**: Find all shortest transformation sequences

**Solution (BFS + DFS):**
```java
public List<List<String>> findLadders(String beginWord, String endWord, List<String> wordList) {
    List<List<String>> result = new ArrayList<>();
    Set<String> wordSet = new HashSet<>(wordList);
    
    if (!wordSet.contains(endWord)) return result;
    
    Map<String, List<String>> graph = new HashMap<>();
    Map<String, Integer> distance = new HashMap<>();
    
    bfs(beginWord, endWord, wordSet, graph, distance);
    dfs(beginWord, endWord, graph, distance, new ArrayList<>(), result);
    
    return result;
}

private void bfs(String beginWord, String endWord, Set<String> wordSet,
                 Map<String, List<String>> graph, Map<String, Integer> distance) {
    Queue<String> queue = new LinkedList<>();
    queue.offer(beginWord);
    distance.put(beginWord, 0);
    
    while (!queue.isEmpty()) {
        String curr = queue.poll();
        int currDist = distance.get(curr);
        
        if (curr.equals(endWord)) continue;
        
        char[] chars = curr.toCharArray();
        for (int i = 0; i < chars.length; i++) {
            char original = chars[i];
            
            for (char c = 'a'; c <= 'z'; c++) {
                chars[i] = c;
                String newWord = new String(chars);
                
                if (wordSet.contains(newWord)) {
                    graph.computeIfAbsent(curr, k -> new ArrayList<>()).add(newWord);
                    
                    if (!distance.containsKey(newWord)) {
                        distance.put(newWord, currDist + 1);
                        queue.offer(newWord);
                    }
                }
            }
            
            chars[i] = original;
        }
    }
}

private void dfs(String curr, String endWord, Map<String, List<String>> graph,
                 Map<String, Integer> distance, List<String> path, List<List<String>> result) {
    path.add(curr);
    
    if (curr.equals(endWord)) {
        result.add(new ArrayList<>(path));
    } else {
        for (String neighbor : graph.getOrDefault(curr, new ArrayList<>())) {
            if (distance.get(neighbor) == distance.get(curr) + 1) {
                dfs(neighbor, endWord, graph, distance, path, result);
            }
        }
    }
    
    path.remove(path.size() - 1);
}

// Time: O(N × M² × 26 + N^L) | Space: O(N × M + N^L)
```

---

### **Level 5: FAANG Level**

#### **9. Sliding Puzzle (LeetCode 773)**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/sliding-puzzle/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 8/10
- **Problem**: Minimum moves to solve 2x3 sliding puzzle

**Solution (BFS with State String):**
```java
public int slidingPuzzle(int[][] board) {
    String target = "123450";
    String start = boardToString(board);
    
    if (start.equals(target)) return 0;
    
    // Neighbors for each position in 2x3 grid
    int[][] neighbors = {
        {1, 3},      // 0
        {0, 2, 4},   // 1
        {1, 5},      // 2
        {0, 4},      // 3
        {1, 3, 5},   // 4
        {2, 4}       // 5
    };
    
    Set<String> visited = new HashSet<>();
    Queue<String> queue = new LinkedList<>();
    queue.offer(start);
    visited.add(start);
    int moves = 0;
    
    while (!queue.isEmpty()) {
        int size = queue.size();
        
        for (int i = 0; i < size; i++) {
            String curr = queue.poll();
            
            if (curr.equals(target)) {
                return moves;
            }
            
            int zeroPos = curr.indexOf('0');
            
            for (int neighbor : neighbors[zeroPos]) {
                String next = swap(curr, zeroPos, neighbor);
                
                if (!visited.contains(next)) {
                    visited.add(next);
                    queue.offer(next);
                }
            }
        }
        
        moves++;
    }
    
    return -1;
}

private String boardToString(int[][] board) {
    StringBuilder sb = new StringBuilder();
    for (int[] row : board) {
        for (int num : row) {
            sb.append(num);
        }
    }
    return sb.toString();
}

private String swap(String s, int i, int j) {
    char[] chars = s.toCharArray();
    char temp = chars[i];
    chars[i] = chars[j];
    chars[j] = temp;
    return new String(chars);
}

// Time: O((m×n)! × m×n) | Space: O((m×n)!)
// m=2, n=3 for this problem
```

**Interview Discussion:**
- **Q**: Why BFS works here?
  - **A**: Each board state is a node, swaps are edges. BFS finds shortest path (minimum moves).
- **Q**: How to represent state?
  - **A**: Convert 2D board to string "123450" for easy hashing.
- **Q**: Time complexity?
  - **A**: O((m×n)!) because there are (m×n)! possible permutations.
- **Q**: Can we optimize?
  - **A**: Yes! Use A* algorithm with heuristic for faster search.

---

## SECTION 7: COMPANY QUESTIONS

### **Google:**
- Word Ladder (BFS on strings)
- Shortest Path in Binary Matrix (Grid BFS)
- 01 Matrix (Multi-source BFS)

### **Meta:**
- Word Ladder II (BFS + DFS)
- Lowest Common Ancestor (Level tracking)
- Binary Tree Level Order (Level BFS)

### **Amazon:**
- Sliding Puzzle (State space BFS)
- Cheapest Flights Within K Stops (BFS with constraint)
- Minimum Knight Moves (Grid BFS)

### **Microsoft:**
- Word Search II (BFS + Trie)
- Find if Path Exists (Basic BFS)
- Keys and Rooms (Connectivity)

### **Netflix:**
- Minimum Depth of Binary Tree (Level BFS)
- Level Order Traversal (Standard BFS)

### **Uber:**
- Shortest Path in Grid (BFS)
- Minimum Time to Visit All Nodes (Multi-source BFS)

### **Airbnb:**
- Find All People With Secret (BFS with time)
- Network Connectivity (Connected components)

### **LinkedIn:**
- Find Closest Connections (BFS levels)
- Connection Suggestions (Graph traversal)

### **Atlassian:**
- Task Dependencies (BFS for topological sort)
- Build Order (Level-based execution)

### **Databricks:**
- Distributed BFS (Parallel graph processing)
- Shortest Path at Scale (Optimized BFS)

---

## SECTION 8: LeetCode Top 20 Must-Solve

### **Easy (6 problems)**

| # | Problem | Link | Frequency | Importance |
|---|---------|------|-----------|------------|
| 1 | Number of Provinces | https://leetcode.com/problems/number-of-provinces/ | 9/10 | 10/10 |
| 2 | Find if Path Exists | https://leetcode.com/problems/find-if-path-exists-in-graph/ | 8/10 | 9/10 |
| 3 | Keys and Rooms | https://leetcode.com/problems/keys-and-rooms/ | 8/10 | 9/10 |
| 4 | Find Center of Star Graph | https://leetcode.com/problems/find-center-of-star-graph/ | 7/10 | 8/10 |
| 5 | Binary Tree Level Order | https://leetcode.com/problems/binary-tree-level-order-traversal/ | 10/10 | 10/10 |
| 6 | Minimum Depth of Binary Tree | https://leetcode.com/problems/minimum-depth-of-binary-tree/ | 9/10 | 10/10 |

---

### **Medium (9 problems)**

| # | Problem | Link | Frequency | Importance |
|---|---------|------|-----------|------------|
| 1 | Shortest Path in Binary Matrix | https://leetcode.com/problems/shortest-path-in-binary-matrix/ | 9/10 | 10/10 |
| 2 | 01 Matrix | https://leetcode.com/problems/01-matrix/ | 9/10 | 10/10 |
| 3 | Word Ladder | https://leetcode.com/problems/word-ladder/ | 10/10 | 10/10 |
| 4 | Cheapest Flights Within K Stops | https://leetcode.com/problems/cheapest-flights-within-k-stops/ | 9/10 | 10/10 |
| 5 | Rotting Oranges | https://leetcode.com/problems/rotting-oranges/ | 10/10 | 10/10 |
| 6 | All Nodes Distance K in Binary Tree | https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree/ | 9/10 | 10/10 |
| 7 | Shortest Path with Alternating Colors | https://leetcode.com/problems/shortest-path-with-alternating-colors/ | 7/10 | 9/10 |
| 8 | Open the Lock | https://leetcode.com/problems/open-the-lock/ | 8/10 | 9/10 |
| 9 | Minimum Fuel Cost to Reach Capital | https://leetcode.com/problems/minimum-fuel-cost-to-reach-capital-city/ | 7/10 | 8/10 |

---

### **Hard (5 problems)**

| # | Problem | Link | Frequency | Importance |
|---|---------|------|-----------|------------|
| 1 | Word Ladder II | https://leetcode.com/problems/word-ladder-ii/ | 9/10 | 10/10 |
| 2 | Sliding Puzzle | https://leetcode.com/problems/sliding-puzzle/ | 8/10 | 9/10 |
| 3 | Shortest Path Visiting All Nodes | https://leetcode.com/problems/shortest-path-visiting-all-nodes/ | 7/10 | 8/10 |
| 4 | Minimum Time to Visit Disappearing Nodes | https://leetcode.com/problems/minimum-time-to-visit-disappearing-nodes/ | 6/10 | 8/10 |
| 5 | Find All People With Secret | https://leetcode.com/problems/find-all-people-with-secret/ | 7/10 | 8/10 |

---

## SECTION 9: Codeforces Mastery

### **Rating 800 (Beginner)**

- **A. Shortest Path of the King** (Codeforces 3A)
  - Why: Basic grid BFS, king moves

### **Rating 1000**

- **B. BFS on Grid** (Codeforces 1063B)
  - Why: Shortest path with obstacles

### **Rating 1200**

- **C. Multi-Source BFS** (Codeforces 1133D)
  - Why: Multiple starting points

### **Rating 1400**

- **D. BFS with State** (Codeforces 1140D)
  - Why: State space search

### **Rating 1600**

- **E. 0-1 BFS** (Codeforces 1133E)
  - Why: Weighted BFS optimization

---

## SECTION 10: Contest Training

### **How to Think Under Pressure:**

1. **Quick Pattern Recognition:**
   - "Shortest path" + unweighted → BFS
   - "Minimum steps" → BFS
   - "Level order" → BFS
   - "Nearest" → Multi-source BFS

2. **Time Management:**
   - Easy BFS: 5-10 minutes
   - Medium BFS: 15-20 minutes
   - Hard BFS: 25-35 minutes

3. **Common Mistakes:**
   - Forgetting to mark visited before enqueue (TLE)
   - Not checking bounds in grid problems
   - Using wrong data structure (Stack instead of Queue)
   - Not handling edge cases (empty graph, single node)

4. **Optimization Tips:**
   - Mark visited immediately when adding to queue
   - Use 1D array instead of 2D for simple grids
   - Early termination when target found
   - Use bidirectional BFS for very large graphs

---

## SECTION 11: Active Learning — 5 Questions

1. **Shortest Path in Binary Matrix** — LeetCode 1091  
   [Link](https://leetcode.com/problems/shortest-path-in-binary-matrix/)

2. **01 Matrix** — LeetCode 542  
   [Link](https://leetcode.com/problems/01-matrix/)

3. **Word Ladder** — LeetCode 127  
   [Link](https://leetcode.com/problems/word-ladder/)

4. **Rotting Oranges** — LeetCode 994  
   [Link](https://leetcode.com/problems/rotting-oranges/)

5. **Open the Lock** — LeetCode 752  
   [Link](https://leetcode.com/problems/open-the-lock/)

---

## SECTION 12: Interview Simulation

**FAANG Interviewer Mode Activated! 🎯**

**Interviewer:** "Let's start with a BFS problem. You're given a binary matrix where 0 represents empty cells and 1 represents obstacles. Find the shortest path from top-left to bottom-right. You can move in 8 directions."

**Candidate (You):** "So we need to use BFS on a grid to find the shortest path, avoiding obstacles?"

**Interviewer:** "Exactly! How would you approach this?"

**[Your turn to explain approach...]**

**Follow-up Questions:**

1. **Clarifying:**
   - Can we move diagonally?
   - What if start or end is blocked?
   - Is the grid always valid?

2. **Edge Cases:**
   - 1×1 grid → return 1
   - Start or end blocked → return -1
   - No path exists → return -1

3. **Complexity:**
   - What's your time complexity?
   - Can you optimize space?
   - What if grid is very large?

4. **Optimization:**
   - Can you use bidirectional BFS?
   - How would you handle weighted grids?
   - What if you need to return the actual path?

**Defend your solution!** 🛡️

---

## SECTION 13: Revision Notes — CHEAT SHEET

### 📝 **BFS Cheat Sheet**

| Concept | Java Code | Time | Space |
|---------|-----------|------|-------|
| **Standard BFS** | `bfs(graph, start)` | O(V+E) | O(V) |
| **Level Order BFS** | `bfsLevelOrder(graph, start)` | O(V+E) | O(V) |
| **BFS with Distance** | `bfsShortestPath(graph, start, target)` | O(V+E) | O(V) |
| **BFS on Grid** | `bfsGrid(grid, start, target)` | O(R×C) | O(R×C) |
| **Multi-Source BFS** | `multiSourceBFS(grid)` | O(R×C) | O(R×C) |

**Key Patterns:**
```java
// Standard BFS Template
public List<Integer> bfs(Graph g, int start) {
    List<Integer> result = new ArrayList<>();
    boolean[] visited = new boolean[g.V];
    Queue<Integer> queue = new LinkedList<>();
    
    visited[start] = true;
    queue.offer(start);
    
    while (!queue.isEmpty()) {
        int curr = queue.poll();
        result.add(curr);
        
        for (int neighbor : g.adjList.get(curr)) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                queue.offer(neighbor);
            }
        }
    }
    
    return result;
}

// BFS with Level Tracking
public List<List<Integer>> bfsLevelOrder(Graph g, int start) {
    List<List<Integer>> result = new ArrayList<>();
    boolean[] visited = new boolean[g.V];
    Queue<Integer> queue = new LinkedList<>();
    
    visited[start] = true;
    queue.offer(start);
    
    while (!queue.isEmpty()) {
        int size = queue.size();
        List<Integer> level = new ArrayList<>();
        
        for (int i = 0; i < size; i++) {
            int curr = queue.poll();
            level.add(curr);
            
            for (int neighbor : g.adjList.get(curr)) {
                if (!visited[neighbor]) {
                    visited[neighbor] = true;
                    queue.offer(neighbor);
                }
            }
        }
        
        result.add(level);
    }
    
    return result;
}

// BFS on Grid (2D)
public int bfsGrid(int[][] grid, int[] start, int[] target) {
    int rows = grid.length;
    int cols = grid[0].length;
    boolean[][] visited = new boolean[rows][cols];
    Queue<int[]> queue = new LinkedList<>();
    
    int[][] directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
    
    queue.offer(start);
    visited[start[0]][start[1]] = true;
    int steps = 0;
    
    while (!queue.isEmpty()) {
        int size = queue.size();
        
        for (int i = 0; i < size; i++) {
            int[] curr = queue.poll();
            int row = curr[0];
            int col = curr[1];
            
            if (row == target[0] && col == target[1]) {
                return steps;
            }
            
            for (int[] dir : directions) {
                int newRow = row + dir[0];
                int newCol = col + dir[1];
                
                if (newRow >= 0 && newRow < rows && 
                    newCol >= 0 && newCol < cols &&
                    !visited[newRow][newCol] &&
                    grid[newRow][newCol] == 0) {
                    
                    visited[newRow][newCol] = true;
                    queue.offer(new int[]{newRow, newCol});
                }
            }
        }
        
        steps++;
    }
    
    return -1;
}

// Multi-Source BFS
public int[][] multiSourceBFS(int[][] grid) {
    int rows = grid.length;
    int cols = grid[0].length;
    int[][] distance = new int[rows][cols];
    Queue<int[]> queue = new LinkedList<>();
    
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (grid[i][j] == 0) {
                queue.offer(new int[]{i, j});
                distance[i][j] = 0;
            } else {
                distance[i][j] = Integer.MAX_VALUE;
            }
        }
    }
    
    int[][] directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
    
    while (!queue.isEmpty()) {
        int[] curr = queue.poll();
        int row = curr[0];
        int col = curr[1];
        
        for (int[] dir : directions) {
            int newRow = row + dir[0];
            int newCol = col + dir[1];
            
            if (newRow >= 0 && newRow < rows && 
                newCol >= 0 && newCol < cols &&
                distance[newRow][newCol] > distance[row][col] + 1) {
                
                distance[newRow][newCol] = distance[row][col] + 1;
                queue.offer(new int[]{newRow, newCol});
            }
        }
    }
    
    return distance;
}
```

---

## SECTION 14: Final Mastery Test

### **Test Questions:**

**Easy (2 problems):**
1. [Keys and Rooms](https://leetcode.com/problems/keys-and-rooms/) — LeetCode 841
2. [Find if Path Exists](https://leetcode.com/problems/find-if-path-exists-in-graph/) — LeetCode 1971

**Medium (2 problems):**
1. [Shortest Path in Binary Matrix](https://leetcode.com/problems/shortest-path-in-binary-matrix/) — LeetCode 1091
2. [Word Ladder](https://leetcode.com/problems/word-ladder/) — LeetCode 127

**Hard (1 problem):**
1. [Sliding Puzzle](https://leetcode.com/problems/sliding-puzzle/) — LeetCode 773

**Contest-Style:**
- Solve in 90 minutes with test cases

**Interview-Style:**
- Explain approach, complexity, edge cases
- Write clean, production-ready code
- Handle follow-up questions
