# 📚 Day 16: Topological Sort — Complete Mastery Guide

## SECTION 1: INTUITION BUILDING (Why Topological Sort Exists?)

### 🤔 **Why This Topic Exists?**

**Real-World Problem:**

> The core idea of Topological Sort is: **Arrange nodes in a directed graph so that for every directed edge `u -> v`, node `u` comes before node `v`**.
>
> In simple terms: If Course B requires Course A as a prerequisite, then Course A must be completed first. Think of this as **dependency-respecting ordering**.

**Real-World Analogies:**
- **Course Prerequisites**: You must pass Calculus I before Calculus II.
- **Build Systems (Make, Webpack, Maven)**: Compile base libraries before compiling the main application.
- **Task Scheduling**: Finish foundation tasks before starting roofing tasks.
- **Recipe Steps**: You must mix the batter before you bake the cake.

If a graph has a cycle (circular dependency), a topological sort is strictly impossible. You cannot finish Course A if it requires Course B, which requires Course C, which requires Course A. That is why topological sort is tightly linked with **cycle detection in directed graphs**.

---

## SECTION 2: COMPLETE THEORY

### 📖 **Core Concepts & Definitions**

**Definition:**
A **topological ordering** of a directed graph is a linear ordering of its vertices such that for every directed edge `u -> v`, vertex `u` appears before vertex `v`. This order only exists for a **Directed Acyclic Graph (DAG)**.

**Core Facts:**
- Only applies to **directed graphs**.
- The graph must be **acyclic** (no cycles).
- Multiple valid topological orders can exist for the same graph.
- Time complexity is **O(V + E)**. Space complexity is **O(V)**.

### **Two Standard Approaches**

1. **Kahn’s Algorithm (BFS-based)**: Relies on `indegree` (incoming edges) counting.
2. **DFS Postorder-based**: Relies on recursive depth-first exploration and reversing the final postorder sequence.

#### **Kahn’s Algorithm (The Queue Method)**
- Compute the `indegree` (number of incoming edges) for every node.
- Push all nodes with an `indegree` of 0 into a Queue.
- Pop a node from the Queue, add it to your valid ordering.
- Decrease the `indegree` of all its outgoing neighbors by 1.
- If a neighbor's `indegree` becomes 0, push it into the Queue.
- **Cycle Check**: If the total nodes processed is less than the total vertices `V`, a cycle exists (the graph is not a DAG).

#### **DFS-Based Topological Sort**
- Perform DFS on each unvisited node.
- Before returning from a node (after exploring all outgoing neighbors), add the node to an array/list (this forms a postorder).
- Reverse the postorder list at the very end to get the topological order.
- **Cycle Check**: If a back edge is found during DFS (reaching a node currently in the recursion stack), a cycle exists.

**Tradeoffs:**
- **Kahn’s Algorithm** is highly intuitive for "course schedule" style problems and naturally extracts the exact build order level-by-level.
- **DFS** is extremely elegant and requires less boilerplate, useful when you already need to traverse the graph deeply.

---

## SECTION 3: PATTERN RECOGNITION

### 🔍 **How to Identify Topological Sort Problems?**

Look for these keywords in problem statements:
- **prerequisite**
- **dependency**
- **order of tasks**
- **build order**
- **compile order**
- **schedule**
- **can finish?**
- **valid ordering**
- **directed acyclic graph**

**When to Use:**
- You need an order that respects prerequisites.
- The graph is directed.
- You need to detect whether a schedule is entirely possible (no cycles).
- You need a valid processing sequence.

**When NOT to Use:**
- Undirected graphs (Topological sort is strictly for directed graphs).
- No dependency or direction implied.
- Pure shortest path problems without dependency constraints.

**Similar-Looking Patterns:**
- **Topological Sort vs DFS Traversal**: Standard DFS traversal does not guarantee dependency order. Postorder-reversed DFS does.
- **Topological Sort vs Shortest Path**: Topo sort orders nodes; it doesn't optimize distance weights.

---

## SECTION 4: VISUAL LEARNING

### **Example Graph**
```text
0 -> 1 -> 3
 \         ^
  -> 2 ----|
```

**Valid topological orders:**
- `0, 2, 1, 3`
- `0, 1, 2, 3`

**Invalid order:**
- `1, 0, 2, 3` (Because `0` must strictly come before `1`).

### **Kahn’s Algorithm Step-by-Step**
```text
Indegree Array:
0: 0
1: 1
2: 1
3: 2

Queue initially: [0]

1. Pop 0 -> Append to answer: [0]
   Decrease indegree of neighbors (1, 2). Both become 0.
   Queue: [1, 2]

2. Pop 1 -> Append to answer: [0, 1]
   Decrease indegree of neighbor (3). It becomes 1.
   Queue: [2]

3. Pop 2 -> Append to answer: [0, 1, 2]
   Decrease indegree of neighbor (3). It becomes 0.
   Queue: [3]

4. Pop 3 -> Append to answer: [0, 1, 2, 3]
   Queue: []

Output: 0, 1, 2, 3
```

### **DFS Postorder Idea**
```text
DFS goes as deep as possible first.
After completely finishing all children of a node, add the node to the output list.
Reverse the output list at the end.
```

---

## SECTION 5: CORE TEMPLATES

### **Template 1: Kahn’s Algorithm (BFS)**
```java
public List<Integer> topologicalSort(int n, List<List<Integer>> graph) {
    int[] indegree = new int[n];
    for (int u = 0; u < n; u++) {
        for (int v : graph.get(u)) {
            indegree[v]++;
        }
    }

    Queue<Integer> q = new LinkedList<>();
    for (int i = 0; i < n; i++) {
        if (indegree[i] == 0) q.offer(i);
    }

    List<Integer> order = new ArrayList<>();

    while (!q.isEmpty()) {
        int node = q.poll();
        order.add(node);

        for (int nei : graph.get(node)) {
            indegree[nei]--;
            if (indegree[nei] == 0) q.offer(nei);
        }
    }

    // Cycle detection check
    if (order.size() != n) return new ArrayList<>(); 
    
    return order;
}
```

### **Template 2: DFS-Based Topological Sort**
```java
public List<Integer> topoSortDFS(int n, List<List<Integer>> graph) {
    int[] state = new int[n]; // 0: unvisited, 1: visiting, 2: visited
    List<Integer> order = new ArrayList<>();

    for (int i = 0; i < n; i++) {
        if (state[i] == 0) {
            if (!dfs(i, graph, state, order)) return new ArrayList<>(); // Cycle found
        }
    }

    Collections.reverse(order); // Reverse postorder
    return order;
}

private boolean dfs(int node, List<List<Integer>> graph, int[] state, List<Integer> order) {
    if (state[node] == 1) return false; // Cycle detected
    if (state[node] == 2) return true;  // Already processed

    state[node] = 1; // Mark visiting
    for (int nei : graph.get(node)) {
        if (!dfs(nei, graph, state, order)) return false;
    }
    
    state[node] = 2; // Mark fully visited
    order.add(node); // Postorder addition
    return true;
}
```

---

## SECTION 6: PROBLEM PROGRESSION & SOLUTIONS

### **Level 1: Very Easy**

#### **1. Course Schedule (LeetCode 207)**
- **Approach**: Cycle detection via Kahn's topological sort. If the graph has a cycle (processed count != `numCourses`), the answer is false.

**Solution (Kahn's):**
```java
public boolean canFinish(int numCourses, int[][] prerequisites) {
    int[] inDegree = new int[numCourses];
    List<List<Integer>> adj = new ArrayList<>();
    for(int i = 0; i < numCourses; i++) adj.add(new ArrayList<>());
    
    for(int[] p : prerequisites) {
        adj.get(p[1]).add(p[0]); // p[1] is prerequisite for p[0]
        inDegree[p[0]]++;
    }
    
    Queue<Integer> q = new LinkedList<>();
    for(int i = 0; i < numCourses; i++) {
        if(inDegree[i] == 0) q.offer(i);
    }
    
    int count = 0;
    while(!q.isEmpty()) {
        int curr = q.poll();
        count++;
        for(int nei : adj.get(curr)) {
            inDegree[nei]--;
            if(inDegree[nei] == 0) q.offer(nei);
        }
    }
    return count == numCourses;
}
```

#### **2. Course Schedule II (LeetCode 210)**
- **Approach**: Exact same approach as Course Schedule, but record the order of nodes processed.

**Solution:**
```java
public int[] findOrder(int numCourses, int[][] prerequisites) {
    int[] inDegree = new int[numCourses];
    List<List<Integer>> adj = new ArrayList<>();
    for(int i = 0; i < numCourses; i++) adj.add(new ArrayList<>());
    
    for(int[] p : prerequisites) {
        adj.get(p[1]).add(p[0]);
        inDegree[p[0]]++;
    }
    
    Queue<Integer> q = new LinkedList<>();
    for(int i = 0; i < numCourses; i++) {
        if(inDegree[i] == 0) q.offer(i);
    }
    
    int[] order = new int[numCourses];
    int idx = 0;
    while(!q.isEmpty()) {
        int curr = q.poll();
        order[idx++] = curr;
        for(int nei : adj.get(curr)) {
            inDegree[nei]--;
            if(inDegree[nei] == 0) q.offer(nei);
        }
    }
    return idx == numCourses ? order : new int[0];
}
```

---

### **Level 2: Easy**

#### **3. Alien Dictionary (LeetCode 269)**
- **Approach**: Build precedence constraints from adjacent words, then topologically sort characters. Essential for FAANG.

**Solution:**
```java
public String alienOrder(String[] words) {
    Map<Character, List<Character>> adj = new HashMap<>();
    Map<Character, Integer> inDegree = new HashMap<>();
    
    // Initialize degrees
    for(String w : words) {
        for(char c : w.toCharArray()) {
            inDegree.put(c, 0);
            adj.put(c, new ArrayList<>());
        }
    }
    
    // Build graph edges
    for(int i = 0; i < words.length - 1; i++) {
        String w1 = words[i], w2 = words[i+1];
        if(w1.length() > w2.length() && w1.startsWith(w2)) return ""; // Invalid prefix contradiction
        
        for(int j = 0; j < Math.min(w1.length(), w2.length()); j++) {
            char c1 = w1.charAt(j), c2 = w2.charAt(j);
            if(c1 != c2) {
                adj.get(c1).add(c2);
                inDegree.put(c2, inDegree.get(c2) + 1);
                break; // Only first differing character matters
            }
        }
    }
    
    // Kahn's Algorithm
    StringBuilder sb = new StringBuilder();
    Queue<Character> q = new LinkedList<>();
    for(char c : inDegree.keySet()) {
        if(inDegree.get(c) == 0) q.offer(c);
    }
    
    while(!q.isEmpty()) {
        char curr = q.poll();
        sb.append(curr);
        for(char nei : adj.get(curr)) {
            inDegree.put(nei, inDegree.get(nei) - 1);
            if(inDegree.get(nei) == 0) q.offer(nei);
        }
    }
    
    return sb.length() == inDegree.size() ? sb.toString() : "";
}
```

#### **4. Minimum Height Trees (LeetCode 310)**
- **Approach**: Think of this as topological sort from the leaves inward (layering). Trim leaves level-by-level until 1 or 2 core nodes remain.

**Solution:**
```java
public List<Integer> findMinHeightTrees(int n, int[][] edges) {
    if (n == 1) return Collections.singletonList(0);
    List<Set<Integer>> adj = new ArrayList<>();
    for(int i = 0; i < n; i++) adj.add(new HashSet<>());
    for(int[] edge : edges) {
        adj.get(edge[0]).add(edge[1]);
        adj.get(edge[1]).add(edge[0]);
    }
    
    List<Integer> leaves = new ArrayList<>();
    for(int i = 0; i < n; i++) {
        if(adj.get(i).size() == 1) leaves.add(i);
    }
    
    while (n > 2) {
        n -= leaves.size();
        List<Integer> newLeaves = new ArrayList<>();
        for (int leaf : leaves) {
            int nei = adj.get(leaf).iterator().next(); // Get sole neighbor
            adj.get(nei).remove(leaf);
            if (adj.get(nei).size() == 1) newLeaves.add(nei);
        }
        leaves = newLeaves;
    }
    return leaves;
}
```

---

### **Level 3: Medium**

#### **5. Parallel Courses (LeetCode 1136)**
- **Approach**: Topological sort with layer counting. Each semester corresponds to one full BFS wave queue drain.

**Solution:**
```java
public int minimumSemesters(int n, int[][] relations) {
    int[] inDegree = new int[n + 1];
    List<List<Integer>> adj = new ArrayList<>();
    for(int i = 0; i <= n; i++) adj.add(new ArrayList<>());
    
    for(int[] r : relations) {
        adj.get(r[0]).add(r[1]);
        inDegree[r[1]]++;
    }
    
    Queue<Integer> q = new LinkedList<>();
    for(int i = 1; i <= n; i++) {
        if(inDegree[i] == 0) q.offer(i);
    }
    
    int semesters = 0, count = 0;
    while(!q.isEmpty()) {
        int size = q.size();
        semesters++;
        for(int i = 0; i < size; i++) {
            int curr = q.poll();
            count++;
            for(int nei : adj.get(curr)) {
                inDegree[nei]--;
                if(inDegree[nei] == 0) q.offer(nei);
            }
        }
    }
    return count == n ? semesters : -1;
}
```

#### **6. Find Eventual Safe States (LeetCode 802)**
- **Approach**: Reverse graph edges + topological elimination of terminal states. Out-degree becomes in-degree.

**Solution:**
```java
public List<Integer> eventualSafeNodes(int[][] graph) {
    int n = graph.length;
    List<List<Integer>> reverseAdj = new ArrayList<>();
    for(int i = 0; i < n; i++) reverseAdj.add(new ArrayList<>());
    int[] inDegree = new int[n];
    
    for(int i = 0; i < n; i++) {
        for(int nei : graph[i]) {
            reverseAdj.get(nei).add(i); // Reverse edges
            inDegree[i]++;              // Original out-degree becomes in-degree
        }
    }
    
    Queue<Integer> q = new LinkedList<>();
    for(int i = 0; i < n; i++) {
        if(inDegree[i] == 0) q.offer(i);
    }
    
    List<Integer> safeNodes = new ArrayList<>();
    while(!q.isEmpty()) {
        int curr = q.poll();
        safeNodes.add(curr);
        for(int nei : reverseAdj.get(curr)) {
            inDegree[nei]--;
            if(inDegree[nei] == 0) q.offer(nei);
        }
    }
    Collections.sort(safeNodes);
    return safeNodes;
}
```

#### **7. Build a Matrix With Conditions (LeetCode 2392)**
- **Approach**: Combines two topological sorts: one for rows, one for columns.

**Solution:**
```java
public int[][] buildMatrix(int k, int[][] rowConditions, int[][] colConditions) {
    int[] rowOrder = topoSort(k, rowConditions);
    int[] colOrder = topoSort(k, colConditions);
    
    if (rowOrder.length == 0 || colOrder.length == 0) return new int[0][0];
    
    int[][] matrix = new int[k][k];
    Map<Integer, Integer> colMap = new HashMap<>();
    for (int i = 0; i < k; i++) colMap.put(colOrder[i], i);
    
    for (int i = 0; i < k; i++) {
        int num = rowOrder[i];
        matrix[i][colMap.get(num)] = num;
    }
    return matrix;
}

private int[] topoSort(int k, int[][] conditions) {
    List<List<Integer>> adj = new ArrayList<>();
    for(int i = 0; i <= k; i++) adj.add(new ArrayList<>());
    int[] inDegree = new int[k + 1];
    
    for(int[] cond : conditions) {
        adj.get(cond[0]).add(cond[1]);
        inDegree[cond[1]]++;
    }
    
    Queue<Integer> q = new LinkedList<>();
    for(int i = 1; i <= k; i++) if(inDegree[i] == 0) q.offer(i);
    
    int[] order = new int[k];
    int idx = 0;
    while(!q.isEmpty()) {
        int curr = q.poll();
        order[idx++] = curr;
        for(int nei : adj.get(curr)) {
            inDegree[nei]--;
            if(inDegree[nei] == 0) q.offer(nei);
        }
    }
    return idx == k ? order : new int[0];
}
```

---

### **Level 4: Hard**

#### **8. Sequence Reconstruction (LeetCode 444)**
- **Approach**: Determine whether a *unique* topological order exists. If the Queue ever holds >1 element, multiple orders exist!

**Solution:**
```java
public boolean sequenceReconstruction(int[] org, List<List<Integer>> seqs) {
    Map<Integer, List<Integer>> adj = new HashMap<>();
    Map<Integer, Integer> inDegree = new HashMap<>();
    
    for (List<Integer> seq : seqs) {
        for (int i = 0; i < seq.size(); i++) {
            adj.putIfAbsent(seq.get(i), new ArrayList<>());
            inDegree.putIfAbsent(seq.get(i), 0);
            if (i > 0) {
                adj.get(seq.get(i-1)).add(seq.get(i));
                inDegree.put(seq.get(i), inDegree.get(seq.get(i)) + 1);
            }
        }
    }
    
    if (inDegree.size() != org.length) return false;
    
    Queue<Integer> q = new LinkedList<>();
    for (int node : inDegree.keySet()) {
        if (inDegree.get(node) == 0) q.offer(node);
    }
    
    int idx = 0;
    while (!q.isEmpty()) {
        if (q.size() > 1) return false; // Must be exactly one unique path
        int curr = q.poll();
        if (org[idx++] != curr) return false;
        
        for (int nei : adj.get(curr)) {
            inDegree.put(nei, inDegree.get(nei) - 1);
            if (inDegree.get(nei) == 0) q.offer(nei);
        }
    }
    return idx == org.length;
}
```

#### **9. Reconstruct Itinerary (LeetCode 332)**
- **Approach**: Graph traversal with lexicographic constraints using Hierholzer's Algorithm (Postorder insertion).

**Solution:**
```java
public List<String> findItinerary(List<List<String>> tickets) {
    Map<String, PriorityQueue<String>> graph = new HashMap<>();
    for(List<String> ticket : tickets) {
        graph.computeIfAbsent(ticket.get(0), k -> new PriorityQueue<>()).add(ticket.get(1));
    }
    
    LinkedList<String> itinerary = new LinkedList<>();
    dfs("JFK", graph, itinerary);
    return itinerary;
}

private void dfs(String airport, Map<String, PriorityQueue<String>> graph, LinkedList<String> itinerary) {
    PriorityQueue<String> nextAirports = graph.get(airport);
    while(nextAirports != null && !nextAirports.isEmpty()) {
        dfs(nextAirports.poll(), graph, itinerary);
    }
    itinerary.addFirst(airport); // Postorder insertion
}
```

---

## SECTION 7: COMPANY QUESTIONS

- **Google**: Course Schedule, Alien Dictionary, Sequence Reconstruction.
- **Meta**: Course Schedule II, Build a Matrix, Safe States.
- **Amazon**: Parallel Courses, Reconstruct Itinerary, Dependency ordering.
- **Apple**: Order constraints, system/build dependencies, graph validation.
- **Netflix**: Production pipeline dependencies, schedule layering.
- **Microsoft**: Prerequisite graphs, alien alphabet, directed cycle checks.
- **Uber**: Task ordering and route constraints.
- **Airbnb**: Itinerary and ordering problems.
- **LinkedIn**: Dependency-based graph ordering.
- **Atlassian**: Build systems, task scheduling, course dependency chains.
- **Databricks**: DAG scheduling, pipeline ordering.

---

## SECTION 8: LeetCode Top 20 Must-Solve

| Category | Problem | Importance | Frequency |
|---|---|---:|---:|
| Easy | Course Schedule | 10/10 | 10/10 |
| Easy | Course Schedule II | 10/10 | 10/10 |
| Easy | Alien Dictionary | 10/10 | 9/10 |
| Medium | Parallel Courses | 9/10 | 8/10 |
| Medium | Find Eventual Safe States | 9/10 | 8/10 |
| Medium | Build a Matrix With Conditions | 8/10 | 7/10 |
| Medium | Minimum Height Trees | 8/10 | 8/10 |
| Medium | Reconstruct Itinerary | 10/10 | 9/10 |
| Medium | Sequence Reconstruction | 9/10 | 8/10 |
| Medium | Longest Increasing Path in a Matrix | 9/10 | 9/10 |
| Hard | Course Schedule IV | 8/10 | 7/10 |
| Hard | Critical Connections | 9/10 | 8/10 |
| Hard | Sort Items by Groups Respecting Dependencies | 9/10 | 8/10 |
| Hard | Alien Dictionary variant questions | 9/10 | 8/10 |
| Hard | Minimum Time to Complete All Tasks | 8/10 | 7/10 |
| Hard | Parallel Courses III | 8/10 | 7/10 |
| Hard | Design Build Order | 8/10 | 7/10 |
| Hard | Validate Unique Ordering | 9/10 | 8/10 |
| Hard | Dependency Resolution problems | 10/10 | 9/10 |
| Hard | Topological layer counting problems | 8/10 | 8/10 |

---

## SECTION 9: Codeforces Mastery

### **Beginner**
- **Rating 800**: Simple DAG order identification.
- **Rating 1000**: Basic course prerequisites.
- **Rating 1200**: Indegree/queue simulation.

### **Intermediate**
- **Rating 1400**: Cyclic dependency detection.
- **Rating 1600**: Lexicographic ordering + topo constraints.

**Why these are chosen:**
These problems make you comfortable with directed dependency graphs and queue-driven ordering, which is the exact mindset required in contest DAG tasks.

---

## SECTION 10: Contest Training

### **Fast Recognition**
If a problem explicitly mentions **prerequisite**, **dependency**, or **order**, immediately think Topological Sort. Ask whether the graph is a DAG. If the answer must include an actual order, Kahn’s algorithm is the most reliable starting point.

### **Time Management**
- **5–10 mins**: Identify graph variables, build `indegrees` array, and build `adjacency` map.
- **10–20 mins**: Implement Kahn’s or DFS.
- **20+ mins**: Handle edge cases (duplicate edges, missing nodes, cycle handling, prefix mismatches).

### **Common Mistakes**
- Forgetting to add initial nodes with an `indegree` of 0 to the Queue.
- Forgetting to check if `processed_count == num_nodes` (Missing cycle detection).
- Building the graph in the wrong direction (`u -> v` instead of `v -> u`).
- Trying to apply Topological Sort to undirected graphs.
- Missing prefix invalidity edge cases (e.g., `["abc", "ab"]`) in string dependency logic like Alien Dictionary.

---

## SECTION 11: Active Learning

Solve these 5 first:
1. [Course Schedule](https://leetcode.com/problems/course-schedule/)
2. [Course Schedule II](https://leetcode.com/problems/course-schedule-ii/)
3. [Alien Dictionary](https://leetcode.com/problems/alien-dictionary/)
4. [Parallel Courses](https://leetcode.com/problems/parallel-courses/)
5. [Find Eventual Safe States](https://leetcode.com/problems/find-eventual-safe-states/)

---

## SECTION 12: Interview Simulation

**FAANG Interviewer Mode Activated! 🎯**

**Interviewer:** “How do you know whether a valid topological sort exists?”

**Candidate (You):**
A strong answer:
- It strictly exists only for Directed Acyclic Graphs (DAGs).
- We verify its existence using Cycle Detection (via Kahn's or DFS).
- In Kahn's, if the final processed count of nodes is strictly less than the total nodes `n`, a circular dependency locked the remaining nodes out of the queue, proving a cycle exists.
- In DFS, if we ever traverse to a node whose state is currently `visiting` (it’s on the current recursion stack), we’ve detected a back edge, proving a cycle.

**Follow-up questions:**
- Why can multiple valid answers exist? (Because multiple nodes can have `indegree == 0` at the same time).
- How do you construct graph direction from textual prerequisites?
- How do you handle disconnected graphs? (Check all nodes up to `n`, independent components will start with `indegree == 0` and process fine).
- How would you verify the uniqueness of an order? (The queue must never exceed a size of `1`).

---

## SECTION 13: Revision Notes — CHEAT SHEET

### **One-page Sheet**
- **Topological sort** is a valid ordering of DAG nodes.
- `u -> v` means `u` must come before `v`.
- **Kahn’s Algorithm** = BFS + Indegree Array + Queue.
- **DFS Topo** = Postorder array addition + Reverse at the end.
- A **Cycle** means NO topological ordering can exist.
- Process count < `n` in Kahn => **Cycle**.
- Strict requirement: **Directed graph only**.
- Best for: Dependencies, scheduling, building arrays.

### **Interview Checklist**
- Is the graph directed?
- Are there prerequisites?
- Do I need the specific order, or just feasibility? (If feasibility, return bool).
- Can there be a cycle? (Always check `count == n`).
- Which model is cleaner: Kahn or DFS? (Kahn is generally cleaner for step-by-step logic in interviews).

---

## SECTION 14: Final Mastery Test

### **Easy**
1. [Course Schedule](https://leetcode.com/problems/course-schedule/) — LeetCode 207
2. [Course Schedule II](https://leetcode.com/problems/course-schedule-ii/) — LeetCode 210

### **Medium**
1. [Alien Dictionary](https://leetcode.com/problems/alien-dictionary/) — LeetCode 269
2. [Parallel Courses](https://leetcode.com/problems/parallel-courses/) — LeetCode 1136
3. [Find Eventual Safe States](https://leetcode.com/problems/find-eventual-safe-states/) — LeetCode 802

### **Hard**
1. [Sequence Reconstruction](https://leetcode.com/problems/sequence-reconstruction/) — LeetCode 444
2. [Reconstruct Itinerary](https://leetcode.com/problems/reconstruct-itinerary/) — LeetCode 332

**Contest-Style:**
- Build a DAG from messy array input and return a valid ordering in 60–90 minutes.

**Interview-Style:**
- Explain Kahn’s algorithm on a whiteboard.
- Explain DFS-based topological sort.
- Explain cycle detection.
- Explain how you’d derive graph direction from prerequisite pairings.
