# Topic 13: Selection Sort

## PROBLEM
Same as before: given an array of numbers, arrange them in ascending order.

```text
Input:  [29, 10, 14, 37, 13]
Output: [10, 13, 14, 29, 37]
```

## BRUTE-FORCE / SIMPLE THINKING
Instead of repeatedly swapping neighbors (bubble sort's approach), think about it differently: **find the smallest element in the entire array, and put it in the first position.** Then find the smallest of what's *left*, and put it in the second position. Repeat until done. This mirrors how a person might sort playing cards by hand — scanning for the smallest each time.

## OBSERVATION
At each step, you only need to do **one swap** — moving the found minimum into its correct position — instead of potentially many swaps per pass like bubble sort. The "sorted region" grows from the **left** this time (opposite of bubble sort, where it grows from the right).

```text
[29, 10, 14, 37, 13]
 ↑ find min in whole array (10), swap into position 0
[10, 29, 14, 37, 13]
     ↑ find min in remaining (13), swap into position 1
[10, 13, 14, 37, 29]
         ↑ find min in remaining (14 — already in place)
[10, 13, 14, 37, 29]
             ↑ find min in remaining (29), swap into position 3
[10, 13, 14, 29, 37]
```

## ALGORITHM
1. For each position `i` from the start of the array:
2. Scan the remaining unsorted portion (`i` to end) to find the **index** of the minimum value.
3. Swap that minimum into position `i`.
4. Move to the next position and repeat, shrinking the unsorted region by one each time.

## DRY RUN

Input: `[5, 2, 8, 1]`

**Step i=0:** unsorted region: `[5, 2, 8, 1]`, min is `1` at index 3.
Swap index 0 and 3 → `[1, 2, 8, 5]`

**Step i=1:** unsorted region: `[2, 8, 5]` (indices 1–3), min is `2` at index 1 — already in place, no swap needed (or a "swap with itself," same result).
→ `[1, 2, 8, 5]`

**Step i=2:** unsorted region: `[8, 5]` (indices 2–3), min is `5` at index 3.
Swap index 2 and 3 → `[1, 2, 5, 8]`

**Step i=3:** unsorted region: `[8]` (index 3 only) — nothing to compare, done.

Final: `[1, 2, 5, 8]` — sorted.

## PSEUDOCODE

```text
for i from 0 to length - 2:
    minIndex = i
    for j from i+1 to length - 1:
        if arr[j] < arr[minIndex]:
            minIndex = j
    if minIndex != i:
        swap arr[i] and arr[minIndex]
```

## MY IMPLEMENTATION
Your turn — translate this pseudocode into JavaScript. Note the key structural difference from bubble sort: here you're tracking an **index** (`minIndex`) across the inner loop, and only swapping **once** after the inner loop finishes — not swapping inside the inner loop itself. Get this distinction right before writing code; it's the most common way people accidentally turn selection sort into a bubble-sort clone.

## COMPLEXITY

- **Time complexity:**
  - Worst case: **O(n²)** — the inner loop always runs fully regardless of input order, since you must scan the whole remaining region every time to find the minimum.
  - Best case: **also O(n²)** — unlike bubble sort, there's no early-exit optimization possible here. Even if the array is already sorted, you still have to scan every remaining element each time to *confirm* the current position holds the minimum.
- **Space complexity:** **O(1)** — sorts in place, only a couple of extra variables (`minIndex`, temp for swap).

> [!NOTE]
> This is an important contrast to flag explicitly: **selection sort is never better than bubble sort's best case**, but it does guarantee at most `n-1` swaps total — useful if swapping is an expensive operation in your specific context (rare in plain JS, but a classic algorithms-course talking point).

## EDGE CASES
- Empty array `[]` — outer loop doesn't run, returns `[]`
- Single-element array `[5]` — outer loop runs zero meaningful iterations (nothing to compare), returns as-is
- Already-sorted array — still runs the full O(n²) scan (no shortcut, unlike bubble sort)
- Duplicate values `[3, 3, 3]` — using `<` (not `<=`) for the comparison means equal elements are never chosen as a "new minimum" over an earlier occurrence, so relative order among equal elements is preserved as much as this algorithm allows
- Array with all identical values — should return unchanged (each "minimum" search finds the same value at the current position)

## OPTIMIZATION DISCUSSION
There isn't a meaningful optimization that improves selection sort's worst or best case time complexity — it's fundamentally O(n²) always, by design (the full scan is required every time). The only real "optimization" people discuss is the **swap-count guarantee**: at most `n-1` swaps total, versus bubble sort's potentially much higher swap count on badly-ordered input. This matters only in contexts where each swap is genuinely expensive (e.g., swapping massive records rather than small numbers) — for typical JS use, this distinction is mostly theoretical/interview-relevant rather than practical.

## WHERE THIS APPEARS IN REAL SOFTWARE
Like bubble sort, selection sort essentially never appears in production sorting — JavaScript's built-in `.sort()` (Phase 9) is far more efficient for real use. Its value is purely educational: understanding the "track an index, swap once per outer iteration" pattern is a stepping stone toward more advanced algorithms, and it's a common contrast question in interviews ("compare bubble sort and selection sort").

## INTERVIEW QUESTION

**Q: How does selection sort differ from bubble sort in terms of how many swaps each performs? Why does selection sort have no meaningful best-case improvement, unlike bubble sort?**

*Answer out loud before checking anything — cover: selection sort does at most `n-1` swaps total (one per outer iteration), while bubble sort can do many more; and selection sort always scans the full remaining region regardless of input order, so there's no way to detect "already sorted" early.*

---

## 💻 WRITE WITHOUT AI

1. Implement `selectionSort(arr)` from the pseudocode above. Test with `[29, 10, 14, 37, 13]` and confirm `[10, 13, 14, 29, 37]`.
2. Add a counter that tracks the total number of **swaps** performed (not comparisons). Test against `[5, 2, 8, 1, 9]` and log the swap count.
3. Test with an already-sorted array `[1, 2, 3, 4, 5]` — confirm it still produces the correct (unchanged) output, and note that it does NOT exit early like bubble sort would.
4. Test with an array of duplicates: `[4, 2, 4, 1, 4]` — confirm correct sorted output `[1, 2, 4, 4, 4]`.
5. (Harder) Modify your implementation to find the **maximum** instead of minimum at each step, building the sorted array from the **end** backward instead of the front. This flips the core "selection" logic — make sure you understand *why* it works, not just pattern-match the min version.

*(Reply with your attempt or where you get stuck before I give hints or the reference solution).*
