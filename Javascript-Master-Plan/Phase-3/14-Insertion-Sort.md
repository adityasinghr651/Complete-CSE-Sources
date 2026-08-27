# Topic 14: Insertion Sort

## PROBLEM
Same problem again: sort an array of numbers in ascending order.

```text
Input:  [12, 11, 13, 5, 6]
Output: [5, 6, 11, 12, 13]
```

## BRUTE-FORCE / SIMPLE THINKING
Think about how you sort a hand of playing cards in real life: you usually keep a "sorted" pile in your hand, and each new card you pick up, you **insert it into the correct position** among the cards you're already holding — shifting others over as needed. That's exactly insertion sort: build up a sorted region one element at a time by inserting each new element where it belongs.

## OBSERVATION
Unlike bubble sort (sorts from the right) or selection sort (finds min/max each time), insertion sort treats the array as **two regions**: a sorted region on the left (starts as just the first element — trivially "sorted") and an unsorted region on the right. Each step takes the **first element of the unsorted region** and inserts it into its correct spot within the sorted region, shifting larger elements rightward to make room.

```text
[12, 11, 13, 5, 6]
 └┬┘  sorted region is just [12] to start (length 1)

Take 11 → belongs before 12 → shift 12 right → [11, 12, 13, 5, 6]
Take 13 → belongs after 12 → no shift needed → [11, 12, 13, 5, 6]
Take 5  → belongs before 11 → shift 11,12,13 right → [5, 11, 12, 13, 6]
Take 6  → belongs after 5, before 11 → shift 11,12,13 right → [5, 6, 11, 12, 13]
```

## ALGORITHM
1. Start from the second element (index 1) — the first element alone is trivially "sorted."
2. Store the current element as `key`.
3. Compare `key` backward against the sorted region (to its left), shifting each larger element one position to the right.
4. Stop shifting once you find an element smaller than (or equal to) `key`, or you reach the start of the array.
5. Insert `key` into the gap left by the shifting.
6. Move to the next unsorted element and repeat.

## DRY RUN

Input: `[5, 2, 4, 1]`

**i=1:** key = `2`. Compare with `5` (to its left) → `5 > 2` → shift `5` right → `[5, 5, 4, 1]`. No more elements to the left. Insert `2` at index 0 → `[2, 5, 4, 1]`

**i=2:** key = `4`. Compare with `5` → `5 > 4` → shift `5` right → `[2, 5, 5, 1]`. Compare with `2` → `2 < 4` → stop. Insert `4` at index 1 → `[2, 4, 5, 1]`

**i=3:** key = `1`. Compare with `5` → shift → `[2, 4, 5, 5]`. Compare with `4` → shift → `[2, 4, 4, 5]`. Compare with `2` → shift → `[2, 2, 4, 5]`. No more elements to left. Insert `1` at index 0 → `[1, 2, 4, 5]`

Final: `[1, 2, 4, 5]` — sorted.

## PSEUDOCODE

```text
for i from 1 to length - 1:
    key = arr[i]
    j = i - 1
    while j >= 0 and arr[j] > key:
        arr[j+1] = arr[j]     // shift right
        j = j - 1
    arr[j+1] = key            // insert key into the gap
```

## MY IMPLEMENTATION
Your turn to translate this into JavaScript. The trickiest part for most people: getting the **inner `while` loop's shifting** right, and correctly placing `key` at `arr[j+1]` *after* the loop ends (not inside it). Trace through your own dry run on paper with a fresh example before coding, to make sure you understand *why* `j+1` is the correct insertion point once the loop stops.

## COMPLEXITY

- **Time complexity:**
  - Worst case (reverse-sorted input): **O(n²)** — every new element has to shift past almost the entire sorted region.
  - Best case (already-sorted input): **O(n)** — the inner `while` condition (`arr[j] > key`) fails immediately every time, so each outer iteration does only one comparison, no shifting.
- **Space complexity:** **O(1)** — sorts in place.

This best-case behavior makes insertion sort meaningfully different from selection sort (always O(n²)) and comparable to bubble-sort-with-early-exit — but insertion sort achieves its O(n) best case naturally, without needing an extra flag/optimization bolted on.

## EDGE CASES
- Empty array `[]` — loop starts at index 1, so on a 0-length array it never runs, returns `[]`
- Single-element array `[5]` — loop starts at index 1, which doesn't exist, so it never runs, returns as-is
- Already-sorted array — best case, O(n), no shifting occurs
- Reverse-sorted array — worst case, maximum shifting every iteration
- Duplicate values `[3, 3, 3]` — using `>` (not `>=`) in the while condition means equal elements are never shifted past each other, preserving their relative order (this makes insertion sort **stable** — worth knowing as a term, since bubble sort is also stable, but you're not required to prove this formally)

## OPTIMIZATION DISCUSSION
Insertion sort already has a strong built-in advantage over selection sort: it adapts to how sorted the input already is — nearly-sorted input runs close to O(n) with almost no shifting. This is why insertion sort is sometimes used as a **fallback for small subarrays** inside more advanced hybrid sorting algorithms (e.g., some real-world implementations of quicksort/timsort switch to insertion sort once a subarray gets small enough, since insertion sort has low overhead for small `n`).

> [!NOTE]
> **EXTENSION — NOT PART OF THE PROVIDED TOPICS**: The specifics of hybrid sorting algorithms like Timsort (used internally by many language sort implementations) aren't part of your syllabus. I'm mentioning it only as real-world context for *why* insertion sort still matters despite being O(n²) in the worst case — not asking you to implement or study Timsort itself.

## WHERE THIS APPEARS IN REAL SOFTWARE
Insertion sort itself is rarely used directly for large datasets, but understanding it matters for two real reasons: (1) it's a common interview topic for comparing sorting strategies and reasoning about best/worst case, and (2) the "shift elements to make room for an insert" pattern shows up conceptually anywhere you maintain a sorted list incrementally (e.g., inserting a new score into an already-sorted leaderboard).

## INTERVIEW QUESTION

**Q: Why does insertion sort perform well on nearly-sorted data, while selection sort does not? Walk through what happens to insertion sort's inner loop when the array is already sorted.**

*Answer out loud before checking anything — the key point: the `while` condition fails on the very first check when data is already in order, so no shifting happens and the outer loop just does a single comparison per element, giving O(n).*

---

## 💻 WRITE WITHOUT AI

1. Implement `insertionSort(arr)` from the pseudocode above. Test with `[12, 11, 13, 5, 6]` and confirm `[5, 6, 11, 12, 13]`.
2. Test with an already-sorted array `[1, 2, 3, 4, 5]` — add a counter for how many **shifts** occur (not comparisons) and confirm it's very low (ideally 0).
3. Test with a reverse-sorted array `[5, 4, 3, 2, 1]` — count the shifts here too, and compare the count against the sorted-array test to see the worst-case-vs-best-case difference concretely.
4. Test with an empty array `[]` and single-element array `[9]` — confirm no crashes.
5. (Harder) Modify `insertionSort` to sort an array of **objects** by a specific numeric property — e.g., `[{ name: "A", score: 50 }, { name: "B", score: 20 }]` sorted ascending by `score`. This forces you to change the comparison (`arr[j].score > key.score`) without changing the overall shifting structure — good test of whether you understand the algorithm or just memorized numeric comparisons.

*(Reply with your attempt or where you get stuck before I give hints or the reference solution).*
