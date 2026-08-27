# Topic 12: Bubble Sort

## PROBLEM
Given an array of numbers in random order, arrange them in ascending order.

```text
Input:  [5, 2, 8, 1, 9]
Output: [1, 2, 5, 8, 9]
```

## BRUTE-FORCE / SIMPLE THINKING
If you had to sort this by hand, a natural instinct is: **compare two neighbors at a time, and swap them if they're in the wrong order.** If you do one full pass through the array doing this, the largest element will "bubble up" to the end. Do enough passes, and eventually everything settles into order.

## OBSERVATION
After **one full pass** comparing every adjacent pair and swapping when needed, the **largest element is guaranteed to reach the last position** — because it will keep "winning" every comparison it's involved in and get pushed rightward until nothing bigger is left to push it further.

This means: after pass 1, the last element is correctly placed. After pass 2, the last *two* are correctly placed. And so on — each pass shrinks the "unsorted" region by one from the right.

```text
Pass 1: [5, 2, 8, 1, 9] → largest (9) bubbles to the end
Pass 2: only need to check the first n-1 elements now
Pass 3: only need to check the first n-2 elements now
...
```

## ALGORITHM
1. Loop over the array.
2. In each pass, compare each pair of adjacent elements.
3. If the left one is bigger than the right one, swap them.
4. Repeat passes until a full pass happens with **no swaps** — meaning the array is already sorted.
5. Each pass can safely ignore the last `i` elements (already sorted from previous passes).

## DRY RUN

Input: `[5, 2, 8, 1]`

**Pass 1:**
```text
[5, 2, 8, 1]
compare 5,2 → swap → [2, 5, 8, 1]
compare 5,8 → no swap → [2, 5, 8, 1]
compare 8,1 → swap → [2, 5, 1, 8]
```
End of pass 1: `[2, 5, 1, 8]` — 8 (largest) is now correctly in last position.

**Pass 2:** (ignore last element, it's settled)
```text
[2, 5, 1, 8]
compare 2,5 → no swap
compare 5,1 → swap → [2, 1, 5, 8]
```
End of pass 2: `[2, 1, 5, 8]`

**Pass 3:** (ignore last two)
```text
[2, 1, 5, 8]
compare 2,1 → swap → [1, 2, 5, 8]
```
End of pass 3: `[1, 2, 5, 8]` — fully sorted. A final pass with no swaps would confirm this and let us stop early.

## PSEUDOCODE

```text
for i from 0 to length - 1:
    swapped = false
    for j from 0 to length - i - 2:
        if arr[j] > arr[j+1]:
            swap arr[j] and arr[j+1]
            swapped = true
    if not swapped:
        break   // already sorted, stop early
```

## MY IMPLEMENTATION
This is where **you** write it — don't peek ahead at hints until you've genuinely tried. Translate the pseudocode above into real JavaScript yourself, using a nested `for` loop and a temporary variable for swapping. Come back with your attempt before I show hints or a reference solution.

## COMPLEXITY

- **Time complexity:**
  - Worst case (reverse-sorted input): **O(n²)** — for every element, you compare against almost every other element.
  - Best case (already sorted, with the early-exit `swapped` check): **O(n)** — one pass detects no swaps and exits immediately.
- **Space complexity:** **O(1)** — sorting happens in place, no extra array needed (aside from a temp variable during swaps).

## EDGE CASES
- Empty array `[]` — should return `[]` without error (loop just never runs)
- Single-element array `[5]` — already "sorted," no swaps needed
- Already-sorted array — with the early-exit optimization, this finishes in one pass (O(n)) instead of wastefully running all passes
- Array with duplicate values `[3, 3, 3]` — should remain stable/unchanged, since `>` (not `>=`) means equal values never trigger a swap
- All elements identical or all in reverse order — worth testing both explicitly

## OPTIMIZATION DISCUSSION
Bubble sort is a **teaching algorithm**, not a production one — real code should never hand-roll bubble sort for actual sorting needs (JS's built-in `.sort()`, covered in Phase 9, uses a far more efficient algorithm internally). Its value here is purely pedagogical: it's the clearest way to *see* how sorting logic works, dry-run by hand, and reason about complexity before moving to smarter approaches (selection sort, insertion sort — next up — and eventually the built-in methods).

The one real optimization worth knowing: the **early-exit `swapped` flag** above. Without it, bubble sort always runs the full `n` passes even on an already-sorted array — an obviously wasteful O(n²) in the best case. With it, best case drops to O(n).

## WHERE THIS APPEARS IN REAL SOFTWARE
Bubble sort itself essentially never appears in production code — it's too slow for anything beyond tiny datasets. Its real value is as an **interview and fundamentals topic**: interviewers use it to check whether you understand nested loops, swapping, and can reason about time complexity from first principles, before discussing better algorithms (merge sort, quicksort) or just using built-in sort methods.

## INTERVIEW QUESTION

**Q: Walk me through how bubble sort works, and explain why its worst-case time complexity is O(n²). What's one simple optimization you can add?**

*Answer out loud before checking anything — use the "largest bubbles to the end each pass" explanation and the early-exit optimization as your two key points.*

---

## 💻 WRITE WITHOUT AI

1. Implement `bubbleSort(arr)` from the pseudocode above. Test with `[5, 2, 8, 1, 9]` and confirm you get `[1, 2, 5, 8, 9]`.
2. Add the early-exit optimization (`swapped` flag) if you didn't already include it. Test it against an already-sorted array like `[1, 2, 3, 4, 5]` and add a `console.log` counting how many passes actually ran — confirm it exits after just 1 pass.
3. Test your implementation against an empty array `[]` and a single-element array `[7]` — confirm no crashes.
4. Test with an array containing duplicates: `[4, 2, 4, 1, 4]` — confirm it sorts correctly to `[1, 2, 4, 4, 4]`.
5. (Harder) Modify your `bubbleSort` to accept a second parameter, `descending` (boolean), that sorts in descending order when `true`, ascending when `false`/omitted — without duplicating the whole function, just adjust the comparison.

*(Reply with your attempt (or where you're stuck) — I'll give hints before any reference solution, per your rules).*
