# PHASE 4 — Recursion & Backtracking + Sliding Window / Two Pointers / Binary Search
### (Parts 7 + Core Patterns fused: these show up constantly together)

This phase covers the meta-patterns of LeetCode. If you can identify *which* of these patterns a problem falls into, the code practically writes itself.

---

## 1. Backtracking

**The Concept:** Backtracking is a systematic way to iterate through all the possible configurations of a search space. It's essentially Recursion + an "Undo" step. 
You make a choice, explore down that path, and then *undo* the choice so you can try the next one.

**Basic Syntax / Template:**
```java
public void backtrack(List<Integer> currentPath) {
    if (/* Base Case: currentPath is a complete valid answer */) {
        result.add(new ArrayList<>(currentPath)); // SAVE A COPY!
        return;
    }
    
    for (/* each choice available */) {
        currentPath.add(choice);      // 1. CHOOSE
        backtrack(currentPath);       // 2. EXPLORE
        currentPath.remove(currentPath.size() - 1); // 3. UNDO (Backtrack!)
    }
}
```

> [!WARNING]
> **The #1 Backtracking Bug**: Forgetting `new ArrayList<>(current)`. If you just do `result.add(current)`, you are saving a reference to the list. When you backtrack and clear the list, your saved answer will also become empty! Always save a copy.

**DSA Application: Subsets**
```java
public void subsets(int[] nums, int idx, List<Integer> current, List<List<Integer>> result) {
    if (idx == nums.length) {
        result.add(new ArrayList<>(current)); // Save copy
        return;
    }
    
    // Choice 1: EXCLUDE the current number
    subsets(nums, idx + 1, current, result);
    
    // Choice 2: INCLUDE the current number
    current.add(nums[idx]);
    subsets(nums, idx + 1, current, result);
    
    // UNDO the inclusion for the next choices up the tree
    current.remove(current.size() - 1);
}
```

**Decision Rule:** "Generate *all* combinations/permutations/subsets" → Backtracking.

---

## 2. Sliding Window (Variable Size)

**The Concept:** A specialized two-pointer technique used for **contiguous subarray or substring** problems. Instead of nested loops O(N²), you use a `left` and `right` pointer to represent a "window" that expands and shrinks, achieving O(N) time.

**Basic Syntax (The Expanding/Shrinking Window):**
```java
int left = 0;
for (int right = 0; right < arr.length; right++) {
    // 1. Add arr[right] to your window state
    
    while (/* Window is invalid */) {
        // 2. Remove arr[left] from your window state
        left++; // Shrink the window
    }
    
    // 3. Update your max/min length
}
```

**DSA Application: Longest Substring Without Repeating Characters**
```java
public int lengthOfLongestSubstring(String s) {
    Set<Character> window = new HashSet<>();
    int left = 0, maxLen = 0;
    
    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);
        
        // While window is invalid (contains duplicate)
        while (window.contains(c)) {
            window.remove(s.charAt(left));
            left++; // Shrink from left
        }
        
        window.add(c); // Expand from right
        maxLen = Math.max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

**Decision Rule:** "Find the longest/shortest **contiguous** subarray or substring" → Sliding Window.

---

## 3. Two Pointers (Converging)

**The Concept:** Using two pointers (usually one at the start, one at the end) moving towards each other. Used heavily on **sorted** arrays to avoid O(N²) loops.

**DSA Application: Two Sum II (Sorted Array)**
```java
public int[] twoSum(int[] numbers, int target) {
    int left = 0, right = numbers.length - 1;
    
    while (left < right) {
        int sum = numbers[left] + numbers[right];
        
        if (sum == target) {
            return new int[]{left + 1, right + 1}; // Found it!
        } else if (sum < target) {
            left++; // Sum too small, move left pointer to a bigger number
        } else {
            right--; // Sum too big, move right pointer to a smaller number
        }
    }
    return new int[]{};
}
```

**Decision Rule:** "Find a pair in a **sorted** array" → Two Pointers.

---

## 4. Binary Search (The Boundary Pattern)

**The Concept:** Binary Search isn't just for finding an exact number like `7` in a sorted array. It's used for finding a **boundary** where a condition goes from `false` to `true`. This is called "Binary Search on the Answer".

**Basic Syntax (The Boundary Pattern):**
```java
int lo = 0, hi = max_possible_value;

while (lo < hi) { // NOTE: < not <=
    int mid = lo + (hi - lo) / 2; // Prevents integer overflow!
    
    if (isValid(mid)) {
        hi = mid; // mid might be the answer, so don't do mid - 1
    } else {
        lo = mid + 1; // mid is definitely invalid
    }
}
return lo; // lo and hi converge on the exact boundary point
```

> [!IMPORTANT]
> **`lo + (hi - lo) / 2`**: Why not `(lo + hi) / 2`? If `lo` and `hi` are both huge numbers (like 2 billion), adding them together overflows the 32-bit integer limit and wraps around to a negative number! The subtraction formula is completely safe.

**Decision Rule:** "Find the *minimum capacity / smallest value* that satisfies a condition" → Binary Search on the Answer.

---

## 🚀 Next Steps
Can you open a blank editor and write the **Sliding Window template** and the **Backtracking subset generator** from memory? Try it!
