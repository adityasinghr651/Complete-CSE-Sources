# 📚 Day 8: HashMap & HashSet — Complete Mastery Guide (Enhanced Edition for Students)

## SECTION 1: COMPLETE DEFINITIONS & CORE CONCEPTS (For Students)

### What is HashMap? 🤔

#### **Definition (Official):**

> **A HashMap** is a data structure that stores **key-value pairs** and provides **O(1) average time complexity** for:
> - **Insert** (`put(key, value)`)
> - **Delete** (`remove(key)`)
> - **Search** (`get(key)`)
>
> It uses **hashing** to map keys to array indices, enabling **fast retrieval** without searching.

**Key Properties:**

| Property | Description | Why Important |
|----------|-------------|---------------|
| **Key-Value Pairs** | Stores data as (key, value) tuples | Example: ("name", "Aditya") |
| **O(1) Operations** | Insert, Delete, Search in constant time | FASTEST lookup! |
| **Unordered** | Elements not stored in sorted order | No iteration order guarantee |
| **Unique Keys** | Each key appears only once | Duplicate keys overwrite |
| **Allow Null Values** | Values can be null | Keys usually cannot be null |

**Visual Diagram:**

```text
HashMap: {"name": "Aditya", "age": 20, "city": "Delhi"}

Key       → Value
─────────────────────
"name"    → "Aditya"
"age"     → 20
"city"    → "Delhi"

get("age") → 20  (O(1) - instant!)
put("score", 95) → Adds new entry  (O(1))
remove("city") → Deletes entry  (O(1))
```

**How It Works Internally (Hashing):**

```text
Array of size 100: [_, _, _, ..., _]

Step 1: Hash the key
hash("age") → 47  (returns index)

Step 2: Store at index
Array[47] = ("age", 20)

Step 3: Get later
hash("age") → 47
Array[47] → ("age", 20) ✓

Time: O(1) because NO searching needed!
```

**Hash Function Explained:**

```text
Key: "age"

Hash Function:
1. Convert each char to number: a=97, g=103, e=101
2. Combine: 97 * 31² + 103 * 31 + 101 = 94747
3. Mod array size: 94747 % 100 = 47

Index = 47 → Store at Array[47]
```

### What is HashSet?

#### **Definition:**

> **A HashSet** is a data structure that stores **only keys** (no values) and provides **O(1) average time complexity** for:
> - **Insert** (`add(element)`)
> - **Delete** (`remove(element)`)
> - **Search** (`contains(element)`)
>
> It's basically a **HashMap without values** - used for **unique element storage** and **fast membership checking**.

**Key Difference:**

| HashMap | HashSet |
|---------|---------|
| Stores **(key, value)** pairs | Stores **only keys** |
| `put(key, value)` | `add(element)` |
| `get(key)` → returns value | `contains(element)` → returns true/false |
| Example: `{"name": "Aditya"}` | Example: `{"apple", "banana", "cherry"}` |

**Relationship:**
```text
HashSet = HashMap internally
  - Keys: Your elements
  - Values: Constant placeholder (e.g., PRESENT)

Java: HashSet uses HashMap internally
C++: std::set uses Red-Black Tree (not hash)
```

---

## SECTION 2: COMPLETE NOTES MAKING POINTS (For Students)

### 📝 **Essential Notes for Exam/Interviews**

#### **Point 1: HashMap vs Other Data Structures**

| Feature | Array | Linked List | Binary Search | **HashMap** |
|---------|-------|-------------|---------------|-------------|
| Access by Index | O(1) | O(n) | - | - |
| Access by Key | O(n) | O(n) | O(log n) | **O(1)** ✅ |
| Insert | O(1) end | O(1) | O(n) | **O(1)** ✅ |
| Delete | O(n) | O(n) | O(n) | **O(1)** ✅ |
| Search | O(n) | O(n) | O(log n) | **O(1)** ✅ |
| Sorted? | No | No | Yes | **No** ❌ |
| Space | O(n) | O(n) | O(n) | **O(n)** |

**When to use HashMap:**
- ✅ Need **fast lookup by key** → O(1)
- ✅ Need **frequency counting** → Natural fit
- ✅ Need **two-sum** pattern → Perfect
- ❌ Need **sorted order** → Use Tree/Array + Sort
- ❌ Need **range queries** → Use BST/Segment Tree

**When to use HashSet:**
- ✅ Need **unique elements only** → Automatic deduplication
- ✅ Need **fast membership check** → O(1)
- ✅ Need **remove duplicates** → Simple
- ❌ Need **key-value mapping** → Use HashMap
- ❌ Need **ordered elements** → Use TreeSet/LinkedHashSet

#### **Point 2: Time Complexity Table**

| Operation | HashMap | HashSet | Explanation |
|-----------|---------|---------|-------------|
| **Insert** | O(1) avg, O(n) worst | O(1) avg, O(n) worst | Hash collision can slow down |
| **Delete** | O(1) avg, O(n) worst | O(1) avg, O(n) worst | Need to find element first |
| **Search** | O(1) avg, O(n) worst | O(1) avg, O(n) worst | Hash lookup |
| **Contains Key** | O(1) avg | - | HashMap specific |
| **Contains Element** | - | O(1) avg | HashSet specific |
| **Size** | O(1) | O(1) | Stored variable |
| **IsEmpty** | O(1) | O(1) | Check size = 0 |

**Why O(1) Average?**

```text
Hash Function: hash(key) → index

No collision:
  - Direct access to Array[index]
  - Time: O(1)

With collision (multiple keys → same index):
  - Array[index] stores linked list
  - Search list: O(k) where k = collisions
  - Average k = 1 (good hash function)
  - Time: O(1) average

Worst case (all keys → same index):
  - Array[index] stores list of size n
  - Search list: O(n)
  - Time: O(n) worst
```

**Collision Resolution:**

```text
Hash collision: hash("age") = 47, hash("name") = 47

Method 1: Chaining (Linked List)
Array[47] → ("age", 20) → ("name", "Aditya") → null

Method 2: Open Addressing
Array[47] = ("age", 20)
Array[48] = ("name", "Aditya")  ← Next available slot
```

#### **Point 3: HASH COLLISION (Most Important Concept)**

#### **Definition:**

> **Hash Collision** occurs when **two different keys** produce the **same hash index**.

**Example:**
```text
Array size: 10

hash("age") = 47 % 10 = 7
hash("name") = 97 % 10 = 7  ← COLLISION!

Both map to Array[7]
```

**Why It's Bad:**
- Slows down operations from O(1) → O(n) worst case
- All elements at same index → must search linked list

**Solutions:**

| Method | How | Pros | Cons |
|--------|-----|------|------|
| **Chaining** | Array[i] = linked list | Simple, handles many collisions | Extra space for pointers |
| **Open Addressing** | Find next empty slot | No extra space | Complex, can fill up |
| **Better Hash** | Use stronger hash function | Reduces collisions | Slower hash computation |

**Java HashMap Uses Chaining:**
```java
class HashMap {
    Node[] table;  // Array of nodes
    
    class Node {
        int key;
        String value;
        Node next;  // Linked list for collisions
    }
}

// Insert with collision:
// Array[47] → ("age", 20) → ("name", "Aditya")
```

#### **Point 4: HASH FUNCTION (How Keys Become Indices)**

**Definition:**

> **A Hash Function** converts a **key** (string, int, object) into an **array index** (integer).

**Properties of Good Hash Function:**

| Property | Description | Example |
|----------|-------------|---------|
| **Deterministic** | Same key → same hash always | hash("age") = 47 always |
| **Uniform** | Keys spread evenly across array | No clustering |
| **Fast** | O(1) computation time | Simple math |
| **Minimize Collisions** | Different keys → different indices | Unique mapping |

**Common Hash Functions:**

**For Integers:**
```java
hash(key) = key % array_size

Example:
key = 47, size = 10
hash = 47 % 10 = 7
```

**For Strings:**
```java
hash(string) = 0
for each char c in string:
    hash = hash * 31 + c  // 31 is prime number
return hash % array_size

Example:
"age" → a=97, g=103, e=101
hash = 0
hash = 0 * 31 + 97 = 97
hash = 97 * 31 + 103 = 3100
hash = 3100 * 31 + 101 = 96201
hash = 96201 % 10 = 1
Index = 1
```

**For Objects:**
```java
hash(object) = hash(object.hashCode())

Java: object.hashCode() → int → hash(int)
```

**Why Prime Numbers?**
- 31, 37, 53 (primes) distribute keys better
- Avoid patterns that cause clustering

#### **Point 5: LOAD FACTOR (When to Resize)**

**Definition:**

> **Load Factor** = `number_of_entries / array_capacity`
>
> When load factor exceeds threshold (usually 0.75), HashMap **resizes** (doubles array size) and **rehashes** all entries.

**Example:**
```text
Initial: capacity = 16, threshold = 0.75 × 16 = 12

Put 12 entries:
  Load factor = 12/16 = 0.75 = threshold
  → Resize! Double capacity to 32
  → Rehash all 12 entries to new array

Put more:
  threshold = 0.75 × 32 = 24
  Resize at 24 entries
```

**Why Resize?**
- Prevents too many collisions
- Maintains O(1) performance

**Resize Cost:**
- O(n) time (rehash all entries)
- But happens infrequently → **Amortized O(1)**

#### **Point 6: COMMON HASHMAP OPERATIONS (Java + C++)**

**Java:**
```java
import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;

// HashMap
HashMap<String, Integer> map = new HashMap<>();

map.put("age", 20);           // O(1) - Insert
map.put("name", "Aditya");    // O(1) - Insert (Note: using String requires changing value type to Object or using a different map for mixed types, let's assume it maps String to Integer here)

int age = map.get("age");     // O(1) - Get value
map.containsKey("age");       // O(1) - Check key

map.remove("age");            // O(1) - Delete
int size = map.size();        // O(1) - Size
boolean empty = map.isEmpty(); // O(1) - Is empty

map.clear();                  // O(n) - Remove all

// Iterate
for (String key : map.keySet()) {
    System.out.println(key + ": " + map.get(key));
}

for (Integer value : map.values()) {
    System.out.println(value);
}

for (Map.Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}

// HashSet
HashSet<String> set = new HashSet<>();

set.add("apple");              // O(1) - Insert
set.add("banana");             // O(1) - Insert

set.contains("apple");         // O(1) - Check
set.remove("apple");           // O(1) - Delete

int setSize = set.size();      // O(1) - Size

// Iterate
for (String item : set) {
    System.out.println(item);
}
```

**C++:**
```cpp
#include <unordered_map>
#include <unordered_set>
#include <iostream>
#include <string>

using namespace std;

// HashMap (unordered_map)
unordered_map<string, int> map;

map["age"] = 20;               // O(1) - Insert
map["score"] = 95;             // O(1) - Insert

int age = map["age"];          // O(1) - Get
if (map.find("age") != map.end()) { /* Found */ }  // O(1) - Check

map.erase("age");              // O(1) - Delete
int size = map.size();         // O(1) - Size

// Iterate
for (auto& entry : map) {
    cout << entry.first << ": " << entry.second << endl;
}

// HashSet (unordered_set)
unordered_set<string> set;

set.insert("apple");            // O(1) - Insert
set.insert("banana");           // O(1) - Insert

if (set.find("apple") != set.end()) { /* Found */ }  // O(1) - Check
set.erase("apple");             // O(1) - Delete

// Iterate
for (const string& item : set) {
    cout << item << endl;
}
```

#### **Point 7: WHEN TO USE HASHMAP (Interview Decision Tree)**

```text
QUESTION STARTS
    ↓
Need fast lookup by key?
    ↓ YES → Use HashMap ✅
    ↓ NO
Need frequency counting?
    ↓ YES → Use HashMap ✅
    ↓ NO
Need two-sum pattern?
    ↓ YES → Use HashMap ✅
    ↓ NO
Need unique elements only?
    ↓ YES → Use HashSet ✅
    ↓ NO
Need sorted order?
    ↓ YES → Use Tree/Array + Sort ❌
    ↓ NO
Need range queries?
    ↓ YES → Use BST/Segment Tree ❌
    ↓ NO
→ Consider other DS (Stack, Queue, Heap, etc)
```

#### **Point 8: COMMON HASHMAP PATTERNS (For Interviews)**

| Pattern | When | Example Problem |
|---------|------|-----------------|
| **Frequency Count** | Count occurrences | Valid Anagram, Group Anagrams |
| **Two-Sum** | Find pair with target | Two Sum, 3 Sum |
| **First/Last Index** | Track position | First Bad Version, Last Occurrence |
| **Duplicate Detection** | Find repeats | Contains Duplicate |
| **Cache** | Store recent results | LRU Cache |
| **Grouping** | Categorize data | Group Strings by Prefix |
| **Memoization** | Store recursion results | Fibonacci, Coin Change |

---

## SECTION 3: 7 MAIN HASHMAP PATTERNS (For Students)

### Pattern 1: Frequency Counting

**When to use:**
- Count how many times each element appears
- Compare frequencies (anagrams, equal counts)

**Template:**
```java
public boolean isValidAnagram(String s, String t) {
    if (s.length() != t.length()) return false;
    
    HashMap<Character, Integer> freq = new HashMap<>();
    
    // Count s
    for (char c : s.toCharArray()) {
        freq.put(c, freq.getOrDefault(c, 0) + 1);
    }
    
    // Decrease with t
    for (char c : t.toCharArray()) {
        if (!freq.containsKey(c)) return false;
        freq.put(c, freq.get(c) - 1);
        if (freq.get(c) < 0) return false;
    }
    
    return true;
}
```

**Time:** O(n) | **Space:** O(k) where k = unique characters

### Pattern 2: Two-Sum

**When to use:**
- Find two numbers that add to target
- Find pair with specific property

**Template:**
```java
public int[] twoSum(int[] nums, int target) {
    HashMap<Integer, Integer> map = new HashMap<>();  // value → index
    
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        
        if (map.containsKey(complement)) {
            return new int[]{map.get(complement), i};
        }
        
        map.put(nums[i], i);
    }
    
    return new int[]{};
}
```

**Time:** O(n) | **Space:** O(n)

### Pattern 3: First/Last Index Tracking

**When to use:**
- Find first occurrence of element
- Find last occurrence
- Track position for output

**Template:**
```java
public int firstBadVersion(int n) {
    HashMap<Integer, Boolean> versions = new HashMap<>();
    
    for (int i = 1; i <= n; i++) {
        if (isBadVersion(i)) {
            return i;  // First bad
        }
    }
    
    return -1;
}

// Last occurrence
public int lastOccurrence(int[] nums, int target) {
    HashMap<Integer, Integer> lastIndex = new HashMap<>();
    
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] == target) {
            lastIndex.put(target, i);
        }
    }
    
    return lastIndex.getOrDefault(target, -1);
}
```

**Time:** O(n) | **Space:** O(k)

### Pattern 4: Duplicate Detection

**When to use:**
- Check if array has duplicates
- Find first duplicate
- Find all duplicates

**Template:**
```java
public boolean containsDuplicate(int[] nums) {
    HashSet<Integer> seen = new HashSet<>();
    
    for (int num : nums) {
        if (seen.contains(num)) {
            return true;  // Duplicate found
        }
        seen.add(num);
    }
    
    return false;
}

// Find all duplicates
public List<Integer> findAllDuplicates(int[] nums) {
    HashSet<Integer> seen = new HashSet<>();
    HashSet<Integer> duplicates = new HashSet<>();
    
    for (int num : nums) {
        if (seen.contains(num)) {
            duplicates.add(num);
        }
        seen.add(num);
    }
    
    return new ArrayList<>(duplicates);
}
```

**Time:** O(n) | **Space:** O(n)

### Pattern 5: Grouping by Property

**When to use:**
- Group strings by prefix
- Group numbers by parity
- Categorize data

**Template:**
```java
public List<List<String>> groupByPrefix(String[] words) {
    HashMap<String, List<String>> groups = new HashMap<>();
    
    for (String word : words) {
        String prefix = word.substring(0, 2);  // First 2 chars
        
        if (!groups.containsKey(prefix)) {
            groups.put(prefix, new ArrayList<>());
        }
        
        groups.get(prefix).add(word);
    }
    
    return new ArrayList<>(groups.values());
}

// Group by even/odd
public List<List<Integer>> groupByParity(int[] nums) {
    HashMap<String, List<Integer>> groups = new HashMap<>();
    groups.put("even", new ArrayList<>());
    groups.put("odd", new ArrayList<>());
    
    for (int num : nums) {
        String key = num % 2 == 0 ? "even" : "odd";
        groups.get(key).add(num);
    }
    
    return new ArrayList<>(groups.values());
}
```

**Time:** O(n) | **Space:** O(n)

### Pattern 6: Memoization (Dynamic Programming)

**When to use:**
- Store recursion results
- Avoid recomputing same values
- Optimize DP solutions

**Template:**
```java
HashMap<Integer, Integer> memo = new HashMap<>();

public int fibonacci(int n) {
    if (n <= 1) return n;
    
    if (memo.containsKey(n)) {
        return memo.get(n);
    }
    
    int result = fibonacci(n - 1) + fibonacci(n - 2);
    memo.put(n, result);
    
    return result;
}
```

**Time:** O(n) (was O(2^n)) | **Space:** O(n)

### Pattern 7: LRU Cache (Design Problem)

**When to use:**
- Implement cache with size limit
- Remove least recently used when full
- Fast get/put

**Template:**
```java
class LRUCache {
    HashMap<Integer, Integer> map;
    LinkedList<Integer> order;
    int capacity;
    
    public LRUCache(int capacity) {
        this.capacity = capacity;
        map = new HashMap<>();
        order = new LinkedList<>();
    }
    
    public int get(int key) {
        if (!map.containsKey(key)) return -1;
        
        order.remove((Integer) key); // Remove from old position (by object to avoid index removal)
        order.add(key);              // Add to end (most recent)
        return map.get(key);
    }
    
    public void put(int key, int value) {
        if (map.containsKey(key)) {
            order.remove((Integer) key);
        } else if (map.size() == capacity) {
            int oldest = order.removeFirst();
            map.remove(oldest);
        }
        
        map.put(key, value);
        order.add(key);
    }
}
```

**Time:** O(1) get/put | **Space:** O(capacity)

---

## SECTION 4: PROBLEM PROGRESSION — ALL LINKS WORKING ✓

### LeetCode Problems

#### Level 1: Very Easy

#### **1. Contains Duplicate**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/contains-duplicate/
- **Company Tags**: Google, Microsoft, Amazon
- **Frequency**: 10/10

**Solution (HashSet):**
```java
public boolean containsDuplicate(int[] nums) {
    HashSet<Integer> seen = new HashSet<>();
    
    for (int num : nums) {
        if (seen.contains(num)) {
            return true;
        }
        seen.add(num);
    }
    
    return false;
}
```

**Time:** O(n) | **Space:** O(n)

---

#### **2. Valid Anagram**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/valid-anagram/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution (Frequency Count):**
```java
public boolean isAnagram(String s, String t) {
    if (s.length() != t.length()) return false;
    
    HashMap<Character, Integer> freq = new HashMap<>();
    
    for (char c : s.toCharArray()) {
        freq.put(c, freq.getOrDefault(c, 0) + 1);
    }
    
    for (char c : t.toCharArray()) {
        if (!freq.containsKey(c)) return false;
        freq.put(c, freq.get(c) - 1);
        if (freq.get(c) < 0) return false;
    }
    
    return true;
}
```

**Time:** O(n) | **Space:** O(k)

---

#### Level 2: Easy

#### **3. Two Sum**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/two-sum/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution (Two-Sum Pattern):**
```java
public int[] twoSum(int[] nums, int target) {
    HashMap<Integer, Integer> map = new HashMap<>();
    
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        
        if (map.containsKey(complement)) {
            return new int[]{map.get(complement), i};
        }
        
        map.put(nums[i], i);
    }
    
    return new int[]{};
}
```

**Time:** O(n) | **Space:** O(n)

---

#### **4. First Unique Number**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/first-unique-number/
- **Company Tags**: Microsoft, Amazon
- **Frequency**: 6/10

**Solution:**
```java
class FirstUnique {
    HashMap<Integer, Integer> freq;
    LinkedList<Integer> order;
    
    public FirstUnique(int[] nums) {
        freq = new HashMap<>();
        order = new LinkedList<>();
        
        for (int num : nums) {
            freq.put(num, freq.getOrDefault(num, 0) + 1);
            order.add(num);
        }
    }
    
    public int showFirstUnique() {
        for (int num : order) {
            if (freq.get(num) == 1) {
                return num;
            }
        }
        return -1;
    }
    
    public void add(int value) {
        freq.put(value, freq.getOrDefault(value, 0) + 1);
        order.add(value);
    }
}
```

**Time:** O(n) showFirstUnique, O(1) add | **Space:** O(n)

---

#### Level 3: Medium

#### **5. Group Anagrams**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/group-anagrams/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution (Grouping Pattern):**
```java
public List<List<String>> groupAnagrams(String[] strs) {
    HashMap<String, List<String>> groups = new HashMap<>();
    
    for (String s : strs) {
        char[] chars = s.toCharArray();
        Arrays.sort(chars);
        String key = new String(chars);  // Sorted string = anagram key
        
        if (!groups.containsKey(key)) {
            groups.put(key, new ArrayList<>());
        }
        
        groups.get(key).add(s);
    }
    
    return new ArrayList<>(groups.values());
}
```

**Time:** O(n × m log m) | **Space:** O(n × m)
- n = number of strings
- m = max string length

---

#### **6. Subarray Sum Equals K**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/subarray-sum-equals-k/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 9/10

**Solution (Prefix Sum + HashMap):**
```java
public int subarraySum(int[] nums, int k) {
    HashMap<Integer, Integer> prefixCount = new HashMap<>();
    prefixCount.put(0, 1);  // Empty subarray
    
    int sum = 0, count = 0;
    
    for (int num : nums) {
        sum += num;
        
        if (prefixCount.containsKey(sum - k)) {
            count += prefixCount.get(sum - k);
        }
        
        prefixCount.put(sum, prefixCount.getOrDefault(sum, 0) + 1);
    }
    
    return count;
}
```

**Time:** O(n) | **Space:** O(n)

---

#### **7. Longest Substring Without Repeating**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/longest-substring-without-repeating-characters/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution (HashMap for Index Tracking):**
```java
public int longestUnique(String s) {
    HashMap<Character, Integer> lastIndex = new HashMap<>();
    int max = 0, left = 0;
    
    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);
        
        if (lastIndex.containsKey(c) && lastIndex.get(c) >= left) {
            left = lastIndex.get(c) + 1;
        }
        
        lastIndex.put(c, right);
        max = Math.max(max, right - left + 1);
    }
    
    return max;
}
```

**Time:** O(n) | **Space:** O(k)

---

#### Level 4: Hard

#### **8. LRUCache**
- **Platform**: LeetCode
- **Link**: https://leetcode.com/problems/lru-cache/
- **Company Tags**: **Google, Meta, Amazon, Microsoft**
- **Frequency**: 10/10

**Solution (HashMap + Linked List):**
```java
class LRUCache {
    class Node {
        int key, value;
        Node prev, next;
        Node(int k, int v) { key = k; value = v; }
    }
    
    HashMap<Integer, Node> map;
    Node head, tail;
    int capacity;
    
    public LRUCache(int capacity) {
        this.capacity = capacity;
        map = new HashMap<>();
        head = new Node(0, 0);
        tail = new Node(0, 0);
        head.next = tail;
        tail.prev = head;
    }
    
    public int get(int key) {
        if (!map.containsKey(key)) return -1;
        
        Node node = map.get(key);
        remove(node);
        insert(node);
        
        return node.value;
    }
    
    public void put(int key, int value) {
        if (map.containsKey(key)) {
            remove(map.get(key));
        } else if (map.size() == capacity) {
            remove(tail.prev);
        }
        
        insert(new Node(key, value));
    }
    
    private void remove(Node node) {
        map.remove(node.key);
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }
    
    private void insert(Node node) {
        map.put(node.key, node);
        node.next = head.next;
        head.next.prev = node;
        head.next = node;
        node.prev = head;
    }
}
```

**Time:** O(1) get/put | **Space:** O(capacity)

---

### CodeChef Problems

#### **9. Frequency Array**
- **Platform**: CodeChef
- **Link**: https://www.codechef.com/problems/FREQARR (search "Frequency Array")
- **Rating**: 800

**Solution:**
```java
public void frequencyArray(int[] nums) {
    HashMap<Integer, Integer> freq = new HashMap<>();
    
    for (int num : nums) {
        freq.put(num, freq.getOrDefault(num, 0) + 1);
    }
    
    for (int key : freq.keySet()) {
        System.out.println(key + ": " + freq.get(key));
    }
}
```

---

### Codeforces Problems

#### **10. Hashing Problem**
- **Platform**: Codeforces
- **Link**: https://codeforces.com/problemset (search "hashing")
- **Rating**: 1000-1200

Practice frequency counting and two-sum patterns.

---

## SECTION 5: LeetCode Top 20 (ALL WORKING LINKS) ✓

### Easy (7 problems)

| # | Problem | Link |
|---|---------|------|
| 1 | Contains Duplicate | https://leetcode.com/problems/contains-duplicate/ |
| 2 | Valid Anagram | https://leetcode.com/problems/valid-anagram/ |
| 3 | Two Sum | https://leetcode.com/problems/two-sum/ |
| 4 | First Bad Version | https://leetcode.com/problems/first-bad-version/ |
| 5 | Intersection of Two Arrays | https://leetcode.com/problems/intersection-of-two-arrays/ |
| 6 | Majority Element | https://leetcode.com/problems/majority-element/ |
| 7 | Single Number | https://leetcode.com/problems/single-number/ |

### Medium (8 problems)

| # | Problem | Link |
|---|---------|------|
| 1 | Group Anagrams | https://leetcode.com/problems/group-anagrams/ |
| 2 | Subarray Sum Equals K | https://leetcode.com/problems/subarray-sum-equals-k/ |
| 3 | Longest Substring Without Repeating | https://leetcode.com/problems/longest-substring-without-repeating-characters/ |
| 4 | 3 Sum | https://leetcode.com/problems/3sum/ |
| 5 | 4 Sum | https://leetcode.com/problems/4sum/ |
| 6 | Coin Change | https://leetcode.com/problems/coin-change/ |
| 7 | Word Pattern | https://leetcode.com/problems/word-pattern/ |
| 8 | Isomorphic Strings | https://leetcode.com/problems/isomorphic-strings/ |

### Hard (5 problems)

| # | Problem | Link |
|---|---------|------|
| 1 | LRU Cache | https://leetcode.com/problems/lru-cache/ |
| 2 | MFU Cache | https://leetcode.com/problems/mfu-cache/ |
| 3 | Minimum Window Substring | https://leetcode.com/problems/minimum-window-substring/ |
| 4 | Subarray Product Less Than K | https://leetcode.com/problems/subarray-product-less-than-k/ |
| 5 | Encode and Decode Strings | https://leetcode.com/problems/encode-and-decode-strings/ |

---

## SECTION 6: Active Learning — 5 Questions

1. **Contains Duplicate** — LeetCode 217  
   [Link](https://leetcode.com/problems/contains-duplicate/)

2. **Valid Anagram** — LeetCode 242  
   [Link](https://leetcode.com/problems/valid-anagram/)

3. **Two Sum** — LeetCode 1  
   [Link](https://leetcode.com/problems/two-sum/)

4. **Group Anagrams** — LeetCode 49  
   [Link](https://leetcode.com/problems/group-anagrams/)

5. **LRU Cache** — LeetCode 146  
   [Link](https://leetcode.com/problems/lru-cache/)

---

**Submit your 5 solutions in Java!**

---

## SECTION 7: Revision Notes — CHEAT SHEET

### 📝 **HashMap & HashSet Cheat Sheet**

| Concept | Java Code | C++ Code | Time |
|---------|-----------|----------|------|
| **Create HashMap** | `HashMap<K,V> map = new HashMap<>();` | `unordered_map<K,V> map;` | - |
| **Create HashSet** | `HashSet<V> set = new HashSet<>();` | `unordered_set<V> set;` | - |
| **Insert** | `map.put(key, value)` | `map[key] = value` | O(1) |
| **Insert (Set)** | `set.add(value)` | `set.insert(value)` | O(1) |
| **Get** | `map.get(key)` | `map[key]` | O(1) |
| **Contains** | `map.containsKey(key)` | `map.find(key) != map.end()` | O(1) |
| **Contains (Set)** | `set.contains(value)` | `set.find(value) != set.end()` | O(1) |
| **Remove** | `map.remove(key)` | `map.erase(key)` | O(1) |
| **Size** | `map.size()` | `map.size()` | O(1) |
| **Iterate Keys** | `for (K k : map.keySet())` | `for (auto& [k,v] : map)` | O(n) |
| **Iterate Values** | `for (V v : map.values())` | `for (auto& v : map)` | O(n) |
| **Default Value** | `map.getOrDefault(key, 0)` | `map.count(key) ? map[key] : 0` | O(1) |

**Common Patterns:**
```java
// Frequency count
HashMap<K, Integer> freq = new HashMap<>();
freq.put(key, freq.getOrDefault(key, 0) + 1);

// Two-sum
HashMap<Integer, Integer> map = new HashMap<>();
if (map.containsKey(target - nums[i])) { /* Found */ }
map.put(nums[i], i);

// Grouping
HashMap<String, List<K>> groups = new HashMap<>();
groups.computeIfAbsent(key, k -> new ArrayList<>()).add(value);
```

---
