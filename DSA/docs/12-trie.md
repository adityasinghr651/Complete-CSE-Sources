# 📚 Day 12: Trie (Prefix Tree) — Complete Mastery Guide

## SECTION 1: COMPLETE DEFINITIONS & CORE CONCEPTS

### What is a Trie (Prefix Tree)? 🤔

#### **Definition (Official):**

> **A Trie** (pronounced "try") is a **tree-like data structure** used to store and search for **strings efficiently**. Also called:
> - **Prefix Tree**
> - **Digital Tree**
> - **Radix Tree**
>
> Each node represents a **character**, and paths from root to leaves represent **words**. All descendants of a node share a **common prefix** of the string associated with that node.

**Key Properties:**

| Property | Description | Why Important |
|----------|-------------|---------------|
| **Root** | Represents empty string | Starting point |
| **Each node** | Represents a character | Character-by-character storage |
| **Path to node** | Represents a prefix | Prefix matching |
| **isEndOfWord** | Marker for complete word | Distinguish "app" vs "apple" |
| **Children** | Array/map of 26 (a-z) | Fast character access |
| **No duplicates** | Same prefix shared | Space efficient for common prefixes |

**Visual Diagram:**

```text
Trie containing: "and", "ant", "dad", "do"

        root
        /   \
       a     d
      / \   / \
     n   n a   o
    / \   |   |
   d   t  d   (end)
  (end)(end)(end)

Words:
- a → n → d = "and" ✓
- a → n → t = "ant" ✓
- d → a → d = "dad" ✓
- d → o = "do" ✓
```

**Example with "apple", "app", "apply":**

```text
Trie containing: "app", "apple", "apply"

        root
        |
        a
        |
        p
        |
        p (endOfWord = true)  ← "app" ends here
        |
        l
        |
        e (endOfWord = true)  ← "apple" ends here
        |
        y (endOfWord = true)  ← "apply" ends here

Words:
- "app" ✓ (node marked)
- "apple" ✓ (node marked)
- "apply" ✓ (node marked)
- "appl" ✗ (not marked)
```

---

## SECTION 2: COMPLETE NOTES MAKING POINTS

### 📝 **Essential Notes for Exam/Interviews**

#### **Point 1: TRIE NODE STRUCTURE**

**Definition:**

> **TrieNode** is the basic unit of a Trie. Each node contains:
> - **Children array/map**: Pointers to next characters (26 for a-z)
> - **isEndOfWord**: Boolean flag marking end of a word

**Java Implementation:**
```java
class TrieNode {
    TrieNode[] children;  // Array of 26 (a-z)
    boolean isEndOfWord;  // Marker for complete word
    
    // Constructor
    TrieNode() {
        children = new TrieNode[26];  // For lowercase a-z
        isEndOfWord = false;
    }
}
```

**C++ Implementation:**
```cpp
struct TrieNode {
    TrieNode* children[26];  // Array of 26 (a-z)
    bool isEndOfWord;        // Marker for complete word
    
    // Constructor
    TrieNode() {
        for (int i = 0; i < 26; i++) {
            children[i] = nullptr;
        }
        isEndOfWord = false;
    }
};
```

**HashMap-based (for all characters):**
```java
class TrieNode {
    Map<Character, TrieNode> children;  // All characters
    boolean isEndOfWord;
    
    TrieNode() {
        children = new HashMap<>();
        isEndOfWord = false;
    }
}
```

**Key Terminology:**

| Keyword | Meaning | Example |
|---------|---------|---------|
| **TrieNode** | Node class | `new TrieNode()` |
| **children** | Array/Map of next nodes | `children['a']` |
| **isEndOfWord** | Boolean marker | `node.isEndOfWord = true` |
| **root** | Starting node | `root = new TrieNode()` |
| **insert** | Add word to trie | `insert("apple")` |
| **search** | Check if word exists | `search("apple")` |
| **startsWith** | Check if prefix exists | `startsWith("app")` |
| **delete** | Remove word | `delete("apple")` |

---

#### **Point 2: INSERT OPERATION**

**Algorithm:**

```text
Step 1: Start from root
curr = root

Step 2: For each character in word
for (char c : word.toCharArray()) {
    int index = c - 'a';  // Convert char to index (0-25)
    
    if (curr.children[index] == null) {
        curr.children[index] = new TrieNode();  // Create new node
    }
    
    curr = curr.children[index];  // Move to next node
}

Step 3: Mark end of word
curr.isEndOfWord = true;

Time: O(L) where L = length of word
Space: O(L) for new nodes
```

**Java Code:**
```java
public void insert(String word) {
    TrieNode curr = root;
    
    for (char c : word.toCharArray()) {
        int index = c - 'a';  // 'a' → 0, 'b' → 1, ..., 'z' → 25
        
        if (curr.children[index] == null) {
            curr.children[index] = new TrieNode();
        }
        
        curr = curr.children[index];
    }
    
    curr.isEndOfWord = true;
}
```

**Visual Animation:**

```text
Insert "app" into empty Trie:

Step 1: root
        root

Step 2: Insert 'a'
        root
        |
        a

Step 3: Insert 'p'
        root
        |
        a
        |
        p

Step 4: Insert 'p' (again)
        root
        |
        a
        |
        p
        |
        p

Step 5: Mark end
        root
        |
        a
        |
        p
        |
        p (endOfWord = true)
```

**Time Complexity:** O(L) where L = length of word

---

#### **Point 3: SEARCH OPERATION**

**Algorithm:**

```text
Step 1: Start from root
curr = root

Step 2: For each character in word
for (char c : word.toCharArray()) {
    int index = c - 'a';
    
    if (curr.children[index] == null) {
        return false;  // Character not found → word doesn't exist
    }
    
    curr = curr.children[index];  // Move to next node
}

Step 3: Check if word ends here
return curr.isEndOfWord;  // Must be marked as end of word
```

**Java Code:**
```java
public boolean search(String word) {
    TrieNode curr = root;
    
    for (char c : word.toCharArray()) {
        int index = c - 'a';
        
        if (curr.children[index] == null) {
            return false;
        }
        
        curr = curr.children[index];
    }
    
    return curr.isEndOfWord;  // Check if complete word exists
}
```

**Visual Animation:**

```text
Search "app" in Trie:

        root
        |
        a
        |
        p
        |
        p (endOfWord = true)

Step 1: Start at root
Step 2: 'a' → move to 'a' node ✓
Step 3: 'p' → move to 'p' node ✓
Step 4: 'p' → move to 'p' node ✓
Step 5: Check isEndOfWord → TRUE ✓

Result: "app" exists ✓
```

**Search "ap" (not a word):**

```text
        root
        |
        a
        |
        p (endOfWord = false)  ← Not marked

Step 1: 'a' → move to 'a' ✓
Step 2: 'p' → move to 'p' ✓
Step 3: Check isEndOfWord → FALSE ✗

Result: "ap" does NOT exist (only prefix) ✗
```

**Time Complexity:** O(L) where L = length of word

---

#### **Point 4: STARTSWITH (PREFIX SEARCH)**

**Algorithm:**

```text
Step 1: Start from root
curr = root

Step 2: For each character in prefix
for (char c : prefix.toCharArray()) {
    int index = c - 'a';
    
    if (curr.children[index] == null) {
        return false;  // Prefix not found
    }
    
    curr = curr.children[index];
}

Step 3: Return true (path exists)
return true;  // Don't need to check isEndOfWord
```

**Java Code:**
```java
public boolean startsWith(String prefix) {
    TrieNode curr = root;
    
    for (char c : prefix.toCharArray()) {
        int index = c - 'a';
        
        if (curr.children[index] == null) {
            return false;
        }
        
        curr = curr.children[index];
    }
    
    return true;  // Prefix exists
}
```

**Visual:**

```text
startsWith("app") in Trie:

        root
        |
        a
        |
        p
        |
        p (endOfWord = true)
        |
        l
        |
        e (endOfWord = true)

"app" exists as prefix → TRUE ✓
"apple" also starts with "app" → TRUE ✓
```

**Time Complexity:** O(L) where L = length of prefix

---

#### **Point 5: DELETE OPERATION**

**Cases:**

| Case | Description | Action |
|------|-------------|--------|
| **Word not in Trie** | Path doesn't exist | Return false |
| **Word is prefix of another** | e.g., "app" in "apple" | Just unmark isEndOfWord |
| **Word has unique suffix** | e.g., "apple" in "app" | Remove nodes |
| **Word has shared suffix** | e.g., "ant" and "and" share "an" | Don't remove shared nodes |

**Algorithm:**

```text
Step 1: Recursive delete
Step 2: Check if node can be removed
- No children AND not end of word → Remove
- Has children OR is end of another word → Keep
```

**Java Code:**
```java
public boolean delete(String word) {
    return deleteHelper(root, word, 0);
}

private boolean deleteHelper(TrieNode node, String word, int index) {
    if (index == word.length()) {
        if (!node.isEndOfWord) return false;  // Word not found
        node.isEndOfWord = false;  // Unmark
        
        // Check if node has no children
        return isEmpty(node);
    }
    
    int charIndex = word.charAt(index) - 'a';
    TrieNode child = node.children[charIndex];
    
    if (child == null) return false;  // Word not found
    
    // Recurse
    boolean shouldDeleteCurrentNode = deleteHelper(child, word, index + 1);
    
    // If child should be deleted
    if (shouldDeleteCurrentNode) {
        node.children[charIndex] = null;  // Remove child
        
        // Check if current node can be deleted
        return isEmpty(node) && !node.isEndOfWord;
    }
    
    return false;
}

private boolean isEmpty(TrieNode node) {
    for (int i = 0; i < 26; i++) {
        if (node.children[i] != null) return false;
    }
    return true;
}
```

**Time Complexity:** O(L) where L = length of word

---

#### **Point 6: TIME COMPLEXITY COMPARISON**

| Operation | Trie | BST | Hash Table |
|-----------|------|-----|------------|
| **Insert** | O(L) | O(L × log N) | O(L) avg |
| **Search** | O(L) | O(L × log N) | O(L) avg |
| **Prefix Search** | O(L) | O(N) | O(N) |
| **Delete** | O(L) | O(L × log N) | O(L) avg |
| **Space** | O(N × L) | O(N × L) | O(N × L) |

**Where:**
- N = number of words
- L = average length of word

**Key Advantage:** Prefix search is O(L) in Trie vs O(N) in BST/Hash

---

## SECTION 3: PROBLEM PROGRESSION

### LeetCode Problems

#### Level 1: Very Easy

#### **1. Implement Trie (Prefix Tree)**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/implement-trie-prefix-tree/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 10/10

**Solution:**
```java
class Trie {
    TrieNode root;
    
    class TrieNode {
        TrieNode[] children;
        boolean isEndOfWord;
        
        TrieNode() {
            children = new TrieNode[26];
            isEndOfWord = false;
        }
    }
    
    public Trie() {
        root = new TrieNode();
    }
    
    public void insert(String word) {
        TrieNode curr = root;
        for (char c : word.toCharArray()) {
            int index = c - 'a';
            if (curr.children[index] == null) {
                curr.children[index] = new TrieNode();
            }
            curr = curr.children[index];
        }
        curr.isEndOfWord = true;
    }
    
    public boolean search(String word) {
        TrieNode curr = root;
        for (char c : word.toCharArray()) {
            int index = c - 'a';
            if (curr.children[index] == null) return false;
            curr = curr.children[index];
        }
        return curr.isEndOfWord;
    }
    
    public boolean startsWith(String prefix) {
        TrieNode curr = root;
        for (char c : prefix.toCharArray()) {
            int index = c - 'a';
            if (curr.children[index] == null) return false;
            curr = curr.children[index];
        }
        return true;
    }
}
```

**Time**: O(L) for all operations | **Space**: O(N × L)

---

#### Level 2: Easy

#### **2. Longest Word in Dictionary**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/longest-word-in-dictionary/
- **Company Tags**: Google, Meta, Amazon
- **Frequency**: 8/10

**Solution:**
```java
class TrieNode {
    TrieNode[] children = new TrieNode[26];
    boolean isEndOfWord;
    String word = "";  // Store complete word
}

public String longestWord(String[] words) {
    TrieNode root = new TrieNode();
    
    // Insert all words
    for (String word : words) {
        insert(root, word);
    }
    
    String result = "";
    dfs(root, result);
    return result;
}

private void insert(TrieNode root, String word) {
    TrieNode curr = root;
    for (char c : word.toCharArray()) {
        int index = c - 'a';
        if (curr.children[index] == null) {
            curr.children[index] = new TrieNode();
        }
        curr = curr.children[index];
    }
    curr.isEndOfWord = true;
    curr.word = word;
}

String result = "";

private void dfs(TrieNode node) {
    if (node.word.length() > result.length() || 
        (node.word.length() == result.length() && node.word.compareTo(result) < 0)) {
        result = node.word;
    }
    
    for (int i = 0; i < 26; i++) {
        if (node.children[i] != null && node.children[i].isEndOfWord) {
            dfs(node.children[i]);
        }
    }
}
```

**Time**: O(N × L) | **Space**: O(N × L)

---

#### **3. Implement Magic Dictionary**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/implement-magic-dictionary/
- **Company Tags**: Google, Meta, Amazon
- **Frequency**: 7/10

**Solution:**
```java
class MagicDictionary {
    TrieNode root;
    
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        boolean isEndOfWord;
    }
    
    public MagicDictionary() {
        root = new TrieNode();
    }
    
    public void buildDict(String[] dictionary) {
        for (String word : dictionary) {
            insert(word);
        }
    }
    
    private void insert(String word) {
        TrieNode curr = root;
        for (char c : word.toCharArray()) {
            int index = c - 'a';
            if (curr.children[index] == null) {
                curr.children[index] = new TrieNode();
            }
            curr = curr.children[index];
        }
        curr.isEndOfWord = true;
    }
    
    public boolean search(String searchWord) {
        return searchHelper(root, searchWord, 0, false);
    }
    
    private boolean searchHelper(TrieNode node, String word, int index, boolean changed) {
        if (index == word.length()) {
            return node.isEndOfWord && changed;
        }
        
        int charIndex = word.charAt(index) - 'a';
        
        // Try changing one character
        if (!changed) {
            for (int i = 0; i < 26; i++) {
                if (i != charIndex && node.children[i] != null) {
                    if (searchHelper(node.children[i], word, index + 1, true)) {
                        return true;
                    }
                }
            }
        }
        
        // Continue with same character
        if (node.children[charIndex] != null) {
            return searchHelper(node.children[charIndex], word, index + 1, changed);
        }
        
        return false;
    }
}
```

**Time**: O(N × L) | **Space**: O(N × L)

---

#### Level 3: Medium

#### **4. Add and Search Word**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/add-and-search-word-data-structure-design/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 9/10

**Solution:**
```java
class WordDictionary {
    TrieNode root;
    
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        boolean isEndOfWord;
    }
    
    public WordDictionary() {
        root = new TrieNode();
    }
    
    public void addWord(String word) {
        TrieNode curr = root;
        for (char c : word.toCharArray()) {
            int index = c - 'a';
            if (curr.children[index] == null) {
                curr.children[index] = new TrieNode();
            }
            curr = curr.children[index];
        }
        curr.isEndOfWord = true;
    }
    
    public boolean search(String word) {
        return searchHelper(root, word, 0);
    }
    
    private boolean searchHelper(TrieNode node, String word, int index) {
        if (index == word.length()) {
            return node.isEndOfWord;
        }
        
        char c = word.charAt(index);
        
        if (c == '.') {
            // Wildcard: try all 26 characters
            for (int i = 0; i < 26; i++) {
                if (node.children[i] != null) {
                    if (searchHelper(node.children[i], word, index + 1)) {
                        return true;
                    }
                }
            }
            return false;
        } else {
            int charIndex = c - 'a';
            if (node.children[charIndex] == null) return false;
            return searchHelper(node.children[charIndex], word, index + 1);
        }
    }
}
```

**Time**: O(N × 26^L) worst | **Space**: O(N × L)

---

#### **5. Replace Words**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/replace-words/
- **Company Tags**: Amazon, Microsoft
- **Frequency**: 7/10

**Solution:**
```java
public String replaceWords(List<String> dictionary, String sentence) {
    TrieNode root = new TrieNode();
    
    // Insert all roots into trie
    for (String rootWord : dictionary) {
        insert(root, rootWord);
    }
    
    String[] words = sentence.split(" ");
    StringBuilder result = new StringBuilder();
    
    for (String word : words) {
        String replacement = findRoot(root, word);
        result.append(replacement != null ? replacement : word);
        result.append(" ");
    }
    
    return result.toString().trim();
}

class TrieNode {
    TrieNode[] children = new TrieNode[26];
    boolean isEndOfWord;
}

private void insert(TrieNode root, String word) {
    TrieNode curr = root;
    for (char c : word.toCharArray()) {
        int index = c - 'a';
        if (curr.children[index] == null) {
            curr.children[index] = new TrieNode();
        }
        curr = curr.children[index];
    }
    curr.isEndOfWord = true;
}

private String findRoot(TrieNode root, String word) {
    TrieNode curr = root;
    StringBuilder sb = new StringBuilder();
    
    for (char c : word.toCharArray()) {
        int index = c - 'a';
        if (curr.children[index] == null) return null;
        
        sb.append(c);
        curr = curr.children[index];
        
        if (curr.isEndOfWord) {
            return sb.toString();  // Found shortest root
        }
    }
    
    return null;
}
```

**Time**: O(N × L + M × L) | **Space**: O(N × L)

---

#### **6. Word Search II**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/word-search-ii/
- **Company Tags**: Google, Meta, Amazon, Microsoft
- **Frequency**: 10/10

**Solution:**
```java
class TrieNode {
    TrieNode[] children = new TrieNode[26];
    String word = null;  // Store complete word at end
}

public List<String> findWords(char[][] board, String[] words) {
    List<String> result = new ArrayList<>();
    
    // Build trie from words
    TrieNode root = new TrieNode();
    for (String word : words) {
        insert(root, word);
    }
    
    // DFS from each cell
    for (int i = 0; i < board.length; i++) {
        for (int j = 0; j < board[0].length; j++) {
            dfs(board, root, i, j, result);
        }
    }
    
    return result;
}

private void insert(TrieNode root, String word) {
    TrieNode curr = root;
    for (char c : word.toCharArray()) {
        int index = c - 'a';
        if (curr.children[index] == null) {
            curr.children[index] = new TrieNode();
        }
        curr = curr.children[index];
    }
    curr.word = word;
}

private void dfs(char[][] board, TrieNode node, int i, int j, List<String> result) {
    if (i < 0 || i >= board.length || j < 0 || j >= board[0].length) return;
    
    char c = board[i][j];
    if (c == '#' || node.children[c - 'a'] == null) return;
    
    node = node.children[c - 'a'];
    
    if (node.word != null) {
        result.add(node.word);
        node.word = null;  // Avoid duplicates
    }
    
    board[i][j] = '#';  // Mark as visited
    
    dfs(board, node, i+1, j, result);
    dfs(board, node, i-1, j, result);
    dfs(board, node, i, j+1, result);
    dfs(board, node, i, j-1, result);
    
    board[i][j] = c;  // Restore
}
```

**Time**: O(M × N × 4^L) | **Space**: O(N × L)

---

#### Level 4: Hard

#### **7. Palindrome Pairs**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/palindrome-pairs/
- **Company Tags**: Google, Meta, Amazon
- **Frequency**: 8/10

**Solution:**
```java
class TrieNode {
    TrieNode[] children = new TrieNode[26];
    List<Integer> wordIndices = new ArrayList<>();  // Indices of words passing through
    int wordIndex = -1;
}

public List<List<Integer>> palindromePairs(String[] words) {
    List<List<Integer>> result = new ArrayList<>();
    TrieNode root = new TrieNode();
    
    // Insert reversed words into trie
    for (int i = 0; i < words.length; i++) {
        insert(root, words[i], i);
    }
    
    // Search for pairs
    for (int i = 0; i < words.length; i++) {
        search(root, words[i], i, result);
    }
    
    return result;
}

private void insert(TrieNode root, String word, int index) {
    TrieNode curr = root;
    String reversed = new StringBuilder(word).reverse().toString();
    
    for (char c : reversed.toCharArray()) {
        int charIndex = c - 'a';
        if (curr.children[charIndex] == null) {
            curr.children[charIndex] = new TrieNode();
        }
        curr = curr.children[charIndex];
        curr.wordIndices.add(index);
    }
    curr.wordIndex = index;
}

private void search(TrieNode root, String word, int index, List<List<Integer>> result) {
    TrieNode curr = root;
    
    for (int i = 0; i < word.length(); i++) {
        if (curr.wordIndex != -1 && isPalindrome(word, i, word.length() - 1)) {
            if (curr.wordIndex != index) {
                result.add(Arrays.asList(index, curr.wordIndex));
            }
        }
        
        int charIndex = word.charAt(i) - 'a';
        if (curr.children[charIndex] == null) return;
        curr = curr.children[charIndex];
    }
    
    if (curr.wordIndex != -1 && curr.wordIndex != index) {
        result.add(Arrays.asList(index, curr.wordIndex));
    }
    
    for (int otherIndex : curr.wordIndices) {
        if (otherIndex != index && isPalindrome(words[otherIndex], 0, words[otherIndex].length() - word.length() - 1)) {
             result.add(Arrays.asList(index, otherIndex));
        }
    }
}

private boolean isPalindrome(String s, int left, int right) {
    while (left < right) {
        if (s.charAt(left++) != s.charAt(right--)) return false;
    }
    return true;
}
```

**Time**: O(N × L²) | **Space**: O(N × L)

---

### CodeChef Problems

#### **8. Trie Operations**
- **Platform**: CodeChef
- **Link**: https://www.codechef.com/learn/course/data-structures/tries
- **Rating**: 800

Practice insert, search, delete operations.

---

### Codeforces Problems

#### **9. Trie Problems**
- **Platform**: Codeforces
- **Link**: https://codeforces.com/problemset?tags=trie
- **Rating**: 1000-1600

Practice XOR trie, string problems.

---

## SECTION 4: LeetCode Top 10

### Easy (3 problems)

| # | Problem | Link |
|---|---------|------|
| 1 | Implement Trie (Prefix Tree) | https://leetcode.com/problems/implement-trie-prefix-tree/ |
| 2 | Longest Word in Dictionary | https://leetcode.com/problems/longest-word-in-dictionary/ |
| 3 | Implement Magic Dictionary | https://leetcode.com/problems/implement-magic-dictionary/ |

---

### Medium (4 problems)

| # | Problem | Link |
|---|---------|------|
| 1 | Add and Search Word | https://leetcode.com/problems/add-and-search-word-data-structure-design/ |
| 2 | Replace Words | https://leetcode.com/problems/replace-words/ |
| 3 | Word Search II | https://leetcode.com/problems/word-search-ii/ |
| 4 | Prefix and Suffix Search | https://leetcode.com/problems/prefix-and-suffix-search/ |

---

### Hard (3 problems)

| # | Problem | Link |
|---|---------|------|
| 1 | Palindrome Pairs | https://leetcode.com/problems/palindrome-pairs/ |
| 2 | Word Filter | https://leetcode.com/problems/prefix-and-suffix-search/ |
| 3 | Stream of Characters | https://leetcode.com/problems/stream-of-characters/ |

---

## SECTION 5: Active Learning — 5 Questions

1. **Implement Trie (Prefix Tree)** — LeetCode 208  
   [Link](https://leetcode.com/problems/implement-trie-prefix-tree/)

2. **Longest Word in Dictionary** — LeetCode 720  
   [Link](https://leetcode.com/problems/longest-word-in-dictionary/)

3. **Add and Search Word** — LeetCode 211  
   [Link](https://leetcode.com/problems/add-and-search-word-data-structure-design/)

4. **Word Search II** — LeetCode 212  
   [Link](https://leetcode.com/problems/word-search-ii/)

5. **Replace Words** — LeetCode 648  
   [Link](https://leetcode.com/problems/replace-words/)

---

## SECTION 6: Revision Notes — CHEAT SHEET

### 📝 **Trie Cheat Sheet**

| Concept | Java Code | C++ Code | Time |
|---------|-----------|----------|------|
| **Insert** | `insert(word)` | `insert(word)` | O(L) |
| **Search** | `search(word)` | `search(word)` | O(L) |
| **StartsWith** | `startsWith(prefix)` | `startsWith(prefix)` | O(L) |
| **Delete** | `delete(word)` | `delete(word)` | O(L) |
| **Node Structure** | `TrieNode[] children` | `TrieNode* children[26]` | - |

**Key Patterns:**

```java
// Basic Trie Implementation
class Trie {
    TrieNode root;
    
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        boolean isEndOfWord;
    }
    
    public Trie() {
        root = new TrieNode();
    }
    
    public void insert(String word) {
        TrieNode curr = root;
        for (char c : word.toCharArray()) {
            int index = c - 'a';
            if (curr.children[index] == null) {
                curr.children[index] = new TrieNode();
            }
            curr = curr.children[index];
        }
        curr.isEndOfWord = true;
    }
    
    public boolean search(String word) {
        TrieNode curr = root;
        for (char c : word.toCharArray()) {
            int index = c - 'a';
            if (curr.children[index] == null) return false;
            curr = curr.children[index];
        }
        return curr.isEndOfWord;
    }
    
    public boolean startsWith(String prefix) {
        TrieNode curr = root;
        for (char c : prefix.toCharArray()) {
            int index = c - 'a';
            if (curr.children[index] == null) return false;
            curr = curr.children[index];
        }
        return true;
    }
}

// Word Search with Trie
public List<String> findWords(char[][] board, String[] words) {
    TrieNode root = buildTrie(words);
    List<String> result = new ArrayList<>();
    
    for (int i = 0; i < board.length; i++) {
        for (int j = 0; j < board[0].length; j++) {
            dfs(board, root, i, j, result);
        }
    }
    
    return result;
}

private TrieNode buildTrie(String[] words) {
    TrieNode root = new TrieNode();
    for (String word : words) {
        TrieNode curr = root;
        for (char c : word.toCharArray()) {
            int index = c - 'a';
            if (curr.children[index] == null) {
                curr.children[index] = new TrieNode();
            }
            curr = curr.children[index];
        }
        curr.word = word;
    }
    return root;
}

private void dfs(char[][] board, TrieNode node, int i, int j, List<String> result) {
    if (i < 0 || i >= board.length || j < 0 || j >= board[0].length) return;
    
    char c = board[i][j];
    if (c == '#' || node.children[c - 'a'] == null) return;
    
    node = node.children[c - 'a'];
    
    if (node.word != null) {
        result.add(node.word);
        node.word = null;
    }
    
    board[i][j] = '#';
    dfs(board, node, i+1, j, result);
    dfs(board, node, i-1, j, result);
    dfs(board, node, i, j+1, result);
    dfs(board, node, i, j-1, result);
    board[i][j] = c;
}

// Autocomplete with Trie
public List<String> autocomplete(String prefix) {
    List<String> result = new ArrayList<>();
    TrieNode node = root;
    
    // Navigate to prefix node
    for (char c : prefix.toCharArray()) {
        int index = c - 'a';
        if (node.children[index] == null) return result;
        node = node.children[index];
    }
    
    // DFS to find all words with this prefix
    dfs(node, prefix, result);
    return result;
}

private void dfs(TrieNode node, String current, List<String> result) {
    if (node.isEndOfWord) {
        result.add(current);
    }
    
    for (int i = 0; i < 26; i++) {
        if (node.children[i] != null) {
            dfs(node.children[i], current + (char)('a' + i), result);
        }
    }
}
```

---

## SECTION 7: KEYWORDS & FUNCTIONS SUMMARY

### 🔑 **ALL KEYWORDS**

| Keyword | Type | Purpose |
|---------|------|---------|
| **Trie** | Data Structure | Prefix tree for strings |
| **TrieNode** | Class | Node unit |
| **children** | Array | Pointers to next characters |
| **isEndOfWord** | Boolean | Marker for complete word |
| **root** | Variable | Starting node |
| **insert** | Operation | Add word to trie |
| **search** | Operation | Check if word exists |
| **startsWith** | Operation | Check if prefix exists |
| **delete** | Operation | Remove word |
| **prefix** | Concept | Common starting characters |
| **autocomplete** | Use Case | Find all words with prefix |
| **word search** | Use Case | Grid + trie combination |
| **backtracking** | Technique | DFS with undo |

---

### ⚙️ **ALL FUNCTIONS**

| Function | Purpose | Time |
|----------|---------|------|
| `insert(word)` | Add word to trie | O(L) |
| `search(word)` | Check if word exists | O(L) |
| `startsWith(prefix)` | Check if prefix exists | O(L) |
| `delete(word)` | Remove word from trie | O(L) |
| `buildTrie(words)` | Build trie from array | O(N × L) |
| `findWords(board, words)` | Word search on grid | O(M × N × 4^L) |
| `autocomplete(prefix)` | Find all words with prefix | O(L + K) |
| `longestWord(dictionary)` | Find longest word | O(N × L) |

---

## SECTION 8: INTERVIEW TIPS

### 💡 **Top Interview Questions on Trie**

1. **When to use Trie?**
   - Prefix-based problems (autocomplete, spell checker)
   - Word dictionary problems
   - String search with wildcards (`.`, `*`)
   - XOR problems with bit manipulation (XOR Trie)

2. **Common Patterns:**
   - **Implement Trie**: Basic insert, search, startsWith
   - **Word Search**: Trie + DFS on grid
   - **Autocomplete**: Trie + DFS to find all words
   - **Wildcard Search**: Trie + backtracking

3. **Optimization Tips:**
   - Use array for lowercase a-z (faster than HashMap)
   - Use HashMap for all characters (unicode support)
   - Store word at end node for autocomplete
   - Mark visited cells in grid problems

4. **Time Complexity:**
   - Insert/Search/StartsWith: O(L)
   - Space: O(N × L) where N = words, L = avg length
   - Prefix search: O(L + K) where K = number of results

---

## SECTION 9: VISUAL EXAMPLES

### Example 1: Insert "cat", "car", "bat"

```text
Initial:
        root

Insert "cat":
        root
        |
        c
        |
        a
        |
        t ✓

Insert "car":
        root
        |
        c
        |
        a
       / \
      t   r ✓
     ✓

Insert "bat":
        root
       /   \
      c     b
      |     |
      a     a
     / \    |
    t   r   t ✓
   ✓   ✓

Final Trie:
- "cat" ✓
- "car" ✓
- "bat" ✓
```

### Example 2: Autocomplete for "ca"

```text
Trie:
        root
        |
        c
        |
        a
       / \
      t   r
     /     \
    s       ✓
   ✓

Search "ca" → navigate to 'a' node
DFS from 'a' → find all words:
- "cats" ✓
- "car" ✓

Result: ["car", "cats"]
```

---

## SECTION 10: PRO TIPS

### ✅ **Do's**

1. Always check if `children[index] == null` before accessing
2. Mark `isEndOfWord` properly after insert
3. Use `c - 'a'` for array indexing (0-25)
4. Store complete word at end node for autocomplete
5. Use DFS for finding all words with prefix

### ❌ **Don'ts**

1. Don't use HashMap unless you need all characters (slower)
2. Don't forget to mark `isEndOfWord = true`
3. Don't confuse `search()` with `startsWith()`
4. Don't access `children[index]` without null check
