# PHASE 3 PROJECT — Algorithm Playground

This project combines everything from Phase 3 (bubble sort, selection sort, insertion sort, recursion, palindrome detection) with the Phase 1/2 fundamentals underneath them (functions, loops, arrays, objects). Still console-based — no DOM yet.

## REQUIREMENTS

Build a small command-center script that lets you run and compare each algorithm you've learned, with visibility into **how much work each one does** — not just the final answer.

1. Implement all three sorting algorithms as separate functions (you already wrote these — reuse them here).
2. Each sorting function should also **count comparisons** made during the sort (a new requirement — you'll need to add a counter inside each).
3. Implement `factorial(n)` and `sumUpTo(n)` recursively (reuse from Topic 15).
4. Implement `isPalindrome(str)` (reuse from Topic 16).
5. Build a small "test runner" that runs all three sorts on the **same input array** and prints a comparison table of comparisons made by each.
6. Run the palindrome checker against a list of test strings and print which ones pass/fail.

## FEATURES

- `bubbleSortCounted(arr)` — returns `{ sorted: [...], comparisons: N }`
- `selectionSortCounted(arr)` — same shape
- `insertionSortCounted(arr)` — same shape
- `runComparison(arr)` — runs all three counted sorts on **copies** of the same array (important — sorting mutates in place, so each algorithm needs its own fresh copy) and logs a comparison table
- `factorial(n)`, `sumUpTo(n)` — recursive, reused from Topic 15
- `isPalindrome(str)` — reused from Topic 16
- `testPalindromes(list)` — loops through an array of strings and logs `"<string>" → true/false` for each

## EXPECTED BEHAVIOR

```text
--- Sorting Comparison on [5, 2, 8, 1, 9, 3] ---
Bubble Sort:    [1, 2, 3, 5, 8, 9]  | comparisons: 15
Selection Sort: [1, 2, 3, 5, 8, 9]  | comparisons: 15
Insertion Sort: [1, 2, 3, 5, 8, 9]  | comparisons: 12

--- Sorting Comparison on [1, 2, 3, 4, 5] (already sorted) ---
Bubble Sort:    [1, 2, 3, 4, 5]  | comparisons: 4
Selection Sort: [1, 2, 3, 4, 5]  | comparisons: 10
Insertion Sort: [1, 2, 3, 4, 5]  | comparisons: 4

--- Recursion ---
factorial(5) = 120
sumUpTo(10) = 55

--- Palindrome Tests ---
"racecar" → true
"hello" → false
"level" → true
"javascript" → false
"" → true
```

*(Your exact comparison counts may differ slightly depending on implementation details — the important thing is that they're being tracked and that the *pattern* holds: on already-sorted input, bubble sort and insertion sort should show noticeably fewer comparisons than on random input, while selection sort's count stays roughly the same regardless of input order. This connects directly back to the complexity discussion from Topics 12–14 — you should be able to explain **why** the numbers come out the way they do.)*

## SUGGESTED FILE STRUCTURE

```text
phase3-algorithm-playground/
  algorithms.js
```

## CONCEPTS BEING TESTED

- All three sorting algorithms, correctly reimplemented with instrumentation (comparison counting) added on top
- Recursion (base case / recursive case correctness)
- Two-pointer technique (palindrome)
- Functions returning objects (`{ sorted, comparisons }`)
- Arrays (copying before mutating — critical here, since reusing the same array across three sorts without copying would corrupt your comparison)
- Loops, conditionals (from Phase 1, used throughout)

## IMPLEMENTATION MILESTONES

1. Take your working `bubbleSort`, `selectionSort`, `insertionSort` from Topics 12–14 and modify each to also return a comparison count — add a counter variable, increment it at the exact line where the comparison happens (this placement matters — miscounting is easy if you increment in the wrong spot).
2. Verify each counted sort still produces a correctly sorted array (don't let the counting logic accidentally break the sorting logic).
3. Write `runComparison(arr)` — for each algorithm, create a **fresh copy** of `arr` before sorting (this is the trickiest part — think about *how* you copy an array; you may recall `slice()` exists from array basics, or you can build a copy manually with a loop since `slice()` technically isn't formally introduced until Phase 9 — either is acceptable here, just be deliberate about it).
4. Run `runComparison` on at least two different arrays: one random, one already sorted. Confirm the comparison counts differ in the expected direction for bubble/insertion sort, and stay roughly flat for selection sort.
5. Wire in your recursive `factorial`/`sumUpTo` functions and log a couple of test calls.
6. Wire in `isPalindrome` and write `testPalindromes(list)` to loop over a test array of strings and log pass/fail for each.

## MANUAL TEST CASES

| Test | Expected |
|---|---|
| `runComparison([5,2,8,1,9,3])` | all three sorts return the same correctly sorted array; comparison counts differ per algorithm |
| `runComparison([1,2,3,4,5])` | bubble & insertion show low comparisons; selection stays roughly the same as unsorted case |
| `factorial(0)` | `1` (define this base case even though Topic 15 exercises used `n===1` — think about why `0! = 1` mathematically, and adjust your base case if needed) |
| `sumUpTo(1)` | `1` |
| `isPalindrome("")` | `true` |
| `isPalindrome("A")` | `true` |
| `testPalindromes(["racecar","hello","level","",""])` | correctly logs true/false per string, no crashes |

## EDGE CASES TO HANDLE

- `runComparison` must not mutate the original input array passed to it — the caller's array should be unchanged after calling it (only the internal copies get sorted)
- `factorial(0)` — decide and correctly implement this base case (it's a genuinely common interview trip-up: many people only handle `n === 1` and get it wrong for `0`)
- Empty array passed to any sort — should return `[]`, `0` comparisons, no crash
- Empty string passed to `isPalindrome` — should return `true`, not crash

## 💻 WHAT YOU SHOULD IMPLEMENT YOURSELF

Everything — no starter code. Pay special attention to:
- **Exactly where** you place the comparison counter increment in each sort — get this wrong and your counts will be meaningless even if the sort itself still works correctly.
- **How you copy arrays** before each sort in `runComparison` — do NOT just assign (`let copy = arr`), since that copies the reference, not the contents (this connects straight back to Phase 2's reference-vs-value lessons).
- The `factorial(0)` edge case — don't just copy your Topic 15 code blindly; check if it actually handles `0` correctly, and fix it if not.

> [!TIP]
> Build it, test against the tables above, and share your code + output when done or stuck — I'll review it, not rewrite it, per your rules.
