# PHASE 4 — Recursion & Backtracking + Sliding Window / Two Pointers / Binary Search
### (Parts 7 (recursion half) + 4 patterns fused: these show up constantly, often in the same problem)

---

## 1. Recursion — the shape, then the tree

**Every recursive function needs**: a base case (when to stop) and a recursive case (how to shrink the problem).

```java
int factorial(int n) {
    if (n <= 1) return 1;         // base case
    return n * factorial(n - 1);  // recursive case
}
```

**Call stack visualization** — this is what's actually happening:
```text
factorial(3)
    ↓ calls
3 × factorial(2)
          ↓ calls
      2 × factorial(1)
                ↓ base case
                return 1
          ↓ unwinds
      2 × 1 = 2
    ↓ unwinds
3 × 2 = 6
```
Each call waits on the stack until its recursive call returns — that's why deep recursion (unbalanced trees, large n) can blow the stack (`StackOverflowError`).

**Passing mutable state down** — for backtracking you'll usually pass a shared `List` or array and mutate it, rather than returning new copies each time:
```java
void helper(List<Integer> current, ...) {
    // add to current
    helper(current, ...);
    // remove from current — the "undo" step, this IS backtracking
}
```

---

## 2. Backtracking — the universal template

Backtracking = recursion + "try something → recurse → undo it → try the next thing." The undo step (removing what you just added) is what makes it backtracking instead of plain recursion.

```java
void backtrack(List<Integer> current, /* other state */) {
    if (/* base case: current is a complete valid answer */) {
        result.add(new ArrayList<>(current)); // COPY — current will keep changing
        return;
    }
    for (/* each choice available at this point */) {
        current.add(choice);          // choose
        backtrack(current, ...);      // explore
        current.remove(current.size() - 1); // un-choose
    }
}
```
**The single most common backtracking bug**: forgetting `new ArrayList<>(current)` when saving a result — you save a *reference* to `current`, and since `current` keeps mutating, every saved "answer" ends up pointing to the same (eventually empty) list.

### Subsets (include/exclude each element)
```java
void subsets(int[] nums, int idx, List<Integer> current, List<List<Integer>> result) {
    if (idx == nums.length) {
        result.add(new ArrayList<>(current));
        return;
    }
    // exclude nums[idx]
    subsets(nums, idx + 1, current, result);
    // include nums[idx]
    current.add(nums[idx]);
    subsets(nums, idx + 1, current, result);
    current.remove(current.size() - 1);
}
```

### Permutations (track what's used)
```java
void permute(int[] nums, boolean[] used, List<Integer> current, List<List<Integer>> result) {
    if (current.size() == nums.length) {
        result.add(new ArrayList<>(current));
        return;
    }
    for (int i = 0; i < nums.length; i++) {
        if (used[i]) continue;
        used[i] = true;
        current.add(nums[i]);
        permute(nums, used, current, result);
        current.remove(current.size() - 1);
        used[i] = false; // undo
    }
}
```

### Combination Sum (reuse allowed, prune with sorted input)
```java
void combinationSum(int[] candidates, int target, int start, List<Integer> current, List<List<Integer>> result) {
    if (target == 0) {
        result.add(new ArrayList<>(current));
        return;
    }
    for (int i = start; i < candidates.length; i++) {
        if (candidates[i] > target) break; // requires candidates sorted — prunes the branch
        current.add(candidates[i]);
        combinationSum(candidates, target - candidates[i], i, current, result); // i, not i+1 — reuse allowed
        current.remove(current.size() - 1);
    }
}
```

**Decision rule**: "generate all valid arrangements/subsets/combinations satisfying a constraint" → backtracking. If the problem instead asks for *count* or *optimal value* (not all arrangements), that's often DP instead (Phase 5).

---

## 3. Two Pointers

Two indices moving through a structure, usually converging or moving in the same direction, to avoid an O(n²) nested loop.

**Converging (sorted array, find a pair):**
```java
int i = 0, j = arr.length - 1;
while (i < j) {
    int sum = arr[i] + arr[j];
    if (sum == target) { /* found */ break; }
    else if (sum < target) i++;
    else j--;
}
```

**Same-direction (remove duplicates in place, fast/slow pointer):**
```java
int slow = 0;
for (int fast = 1; fast < arr.length; fast++) {
    if (arr[fast] != arr[slow]) {
        slow++;
        arr[slow] = arr[fast];
    }
}
// arr[0..slow] is the deduplicated result
```

**Decision rule**: "sorted input, find a pair/triple meeting a condition" or "in-place array modification without extra space" → two pointers.

---

## 4. Sliding Window

A specialized two-pointer technique for **contiguous subarray/substring** problems — the window `[left, right]` expands and contracts based on a condition, and you almost always track window state in a `HashMap`/frequency array (Phase 2) rather than recomputing it each time.

**Variable-size window (longest substring without repeating characters):**
```java
Map<Character, Integer> lastSeen = new HashMap<>();
int left = 0, maxLen = 0;
for (int right = 0; right < s.length(); right++) {
    char c = s.charAt(right);
    if (lastSeen.containsKey(c) && lastSeen.get(c) >= left) {
        left = lastSeen.get(c) + 1;   // shrink window past the duplicate
    }
    lastSeen.put(c, right);
    maxLen = Math.max(maxLen, right - left + 1);
}
```

**Fixed-size window (e.g. max sum of any window of size k):**
```java
int windowSum = 0;
for (int i = 0; i < k; i++) windowSum += arr[i]; // prime the first window
int maxSum = windowSum;
for (int right = k; right < arr.length; right++) {
    windowSum += arr[right] - arr[right - k]; // slide: add new, drop old
    maxSum = Math.max(maxSum, windowSum);
}
```

**Decision rule**: "contiguous subarray/substring satisfying some condition, find longest/shortest/count" → sliding window. Fixed size known upfront → fixed window. Size depends on a condition → variable window with expand/shrink logic.

---

## 5. Binary Search

Not just for "find X in sorted array" — also for "find the boundary point in a monotonic search space" (this second use is what trips people up).

**Classic (find exact value):**
```java
int lo = 0, hi = arr.length - 1;
while (lo <= hi) {
    int mid = lo + (hi - lo) / 2;   // avoids overflow vs (lo+hi)/2
    if (arr[mid] == target) return mid;
    else if (arr[mid] < target) lo = mid + 1;
    else hi = mid - 1;
}
return -1;
```
**`lo + (hi - lo) / 2` instead of `(lo + hi) / 2`** — the latter can overflow `int` if `lo` and `hi` are both large. Memorize the safe form.

**Boundary search (find first element satisfying a condition — "search on the answer"):**
```java
int lo = 0, hi = n - 1;
while (lo < hi) {           // note: < not <=, and no -1/+1 return
    int mid = lo + (hi - lo) / 2;
    if (condition(mid)) hi = mid;       // mid might be the answer, keep it in range
    else lo = mid + 1;
}
return lo; // lo == hi, the first index where condition(mid) is true
```
This shape (loop with `lo < hi`, no `-1` sentinel, converges to a single point) is what you use for problems like "find minimum capacity to ship packages in D days," "find peak element," "search in rotated sorted array" (with a modified condition check) — the array doesn't even need to be literally sorted, just the *condition* needs to be monotonic (false...false...true...true).

**Decision rule**: "sorted array, find exact value" → classic binary search. "Find the smallest/largest value satisfying some yes/no condition that flips once as you scan" → boundary/"search on the answer" binary search. `Arrays.binarySearch()` (Phase 1) only covers the classic case.

---

## 6. Updated "I need to..." → tool table

| I need to... | Use |
|---|---|
| Generate all subsets/permutations/combinations | Backtracking (choose → recurse → undo) |
| Save a backtracking result | `new ArrayList<>(current)` — never save the reference directly |
| Find a pair in a sorted array meeting a condition | Two pointers (converging) |
| Dedupe/compact an array in place | Two pointers (same direction, fast/slow) |
| Longest/shortest contiguous substring/subarray meeting a condition | Sliding window (variable size) |
| Max/min over all fixed-size windows | Sliding window (fixed size) |
| Find exact value in sorted array | Binary search (classic) |
| Find boundary where a yes/no condition flips | Binary search on the answer (`lo < hi` shape) |
| Avoid overflow computing a midpoint | `lo + (hi - lo) / 2` |
