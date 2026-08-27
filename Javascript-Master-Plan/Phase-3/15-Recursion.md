# Topic 15: Recursion

## PROBLEM
Some problems are naturally defined **in terms of themselves, but smaller**. Calculating `factorial(5)` means `5 * factorial(4)`, which means `4 * factorial(3)`, and so on. Traversing a nested folder structure means "process this folder, then process each subfolder the same way." Some problems become much cleaner to express by having a function **call itself** with a smaller version of the problem, instead of using loops.

## WHAT
Recursion is when a function calls itself to solve a smaller instance of the same problem, until it reaches a case simple enough to answer directly (the **base case**), at which point the calls start returning back up the chain.

## MENTAL MODEL

Every recursive function needs exactly two things:

```text
BASE CASE       → the stopping condition; answers directly, no further recursion
RECURSIVE CASE  → breaks the problem into a smaller version of itself, calls the function again
```

Without a base case (or a bug that never reaches it), recursion never stops — it runs until it crashes with a **stack overflow** error.

### THE CALL STACK

Think of the call stack as a **stack of plates** — each function call is placed on top, and only the top plate can be removed (returned from) at a time.

```javascript
function factorial(n) {
    if (n === 1) return 1;      // BASE CASE
    return n * factorial(n-1);  // RECURSIVE CASE
}

factorial(3)
```

**Going down (calls pile up):**
```text
factorial(3)
   ↓ calls
   3 * factorial(2)
              ↓ calls
              2 * factorial(1)
                        ↓
                     BASE CASE: return 1
```

**Coming back up (returns unwind):**
```text
factorial(1) returns 1
factorial(2) returns 2 * 1 = 2
factorial(3) returns 3 * 2 = 6

Final result: 6
```

Visualized as a stack of plates (bottom to top = order of calls):

```text
   ┌─────────────────┐
   │ factorial(1) → 1  │  ← top of stack, resolves first
   ├─────────────────┤
   │ factorial(2)      │  ← waiting on factorial(1)
   ├─────────────────┤
   │ factorial(3)      │  ← waiting on factorial(2), bottom of stack
   └─────────────────┘
```

Each call **waits** (stays on the stack, paused) until the call it made returns a value — then it uses that value to compute its own result and returns, popping itself off the stack.

## SYNTAX

```javascript
function factorial(n) {
    if (n === 1) {           // base case
        return 1;
    }
    return n * factorial(n - 1); // recursive case
}

console.log(factorial(5)); // 120
```

## SMALLEST POSSIBLE EXAMPLE

```javascript
function countdown(n) {
    if (n <= 0) {
        console.log("done");
        return;
    }
    console.log(n);
    countdown(n - 1);
}

countdown(3);
// 3
// 2
// 1
// done
```

## MANUAL IMPLEMENTATION
The essential manual skill here is **drawing the call stack diagram yourself** for any recursive function before trusting your intuition about what it does. Take `countdown(3)` above and draw out each call being pushed, then popped, on paper — do this for every recursive exercise below before running the code.

## PRACTICAL USE
- Traversing nested/tree-like data (folder structures, nested comments, nested categories) — a natural fit given recursion mirrors the nested structure itself
- Certain mathematical definitions (factorial, Fibonacci) that are naturally self-referential
- Divide-and-conquer algorithms (not on your syllabus by name, but the general idea: break a problem into smaller identical subproblems)
- Recursive UI structures (e.g., a comment thread where replies can have replies) — relevant once you reach React, since components can render other instances of themselves

## EDGE CASES

1. **Missing or unreachable base case → stack overflow**:
```javascript
function broken(n) {
    return n * broken(n - 1); // no base case at all!
}
broken(5); // RangeError: Maximum call stack size exceeded
```

2. **Base case with wrong condition** — e.g., checking `n === 0` when `n` can skip past 0 (like going `n - 2` each time on an odd starting number) also causes infinite recursion.

3. **Very deep recursion can overflow the stack even with a correct base case**, if the input is too large — JS engines have a limited call stack size (typically a few thousand to ~10,000+ frames depending on the engine):
```javascript
function countdown(n) {
    if (n <= 0) return;
    countdown(n - 1);
}
countdown(1000000); // RangeError: Maximum call stack size exceeded
```
This is a real practical limitation — recursion isn't "free," each call consumes stack memory until it returns.

4. **Off-by-one in the recursive step** — e.g., calling `factorial(n)` instead of `factorial(n - 1)` inside itself never shrinks the problem, same effect as a missing base case.

## COMMON MISTAKES
- Forgetting the base case entirely
- Writing a base case that the recursive step can "jump over" (never exactly matches)
- Forgetting to `return` the recursive call's result (calling it but discarding the returned value)
- Assuming recursion is always more efficient than a loop — for many simple cases, a loop is actually more memory-efficient (no stack growth) and just as clear; recursion shines when the problem structure is naturally recursive (trees, nested data), not as a default choice

## DEBUGGING EXERCISE

```javascript
function sumUpTo(n) {
    if (n === 0) {
        return 0;
    }
    sumUpTo(n - 1) + n;
}

console.log(sumUpTo(5));
```

> [!NOTE]
> *(Reason through it before scrolling: syntax error, runtime error, or logical error? What actually happens?)*
> 
> **Answer:** No syntax error, no crash — but a logical error. The recursive case computes `sumUpTo(n - 1) + n` but never has a `return` in front of it, so the function implicitly returns `undefined` for every call except the base case. `console.log(sumUpTo(5))` prints `undefined`, not `15`. This is one of the most common recursion bugs: doing the recursive call correctly but forgetting to `return` its combined result.

## WHERE THIS APPEARS IN REAL SOFTWARE
- Traversing nested UI trees (the DOM itself is a tree — recursive traversal is how browsers, and libraries, walk it)
- Recursive rendering of nested comment threads or nested category menus
- File system traversal (walking directories and subdirectories)
- Recursive descent parsers (used in building things like JSON parsers or programming language interpreters) — advanced context, not something you need to build now

> **WHERE YOU WILL SEE THIS LATER:**
> ```text
> Recursion
>    ↓
> Recursive React components (a "Tree" or "Comment" component that renders itself for nested children)
>    ↓
> Recursive data traversal in Node.js (e.g., walking a nested JSON config or file tree)
> ```

## INTERVIEW QUESTION

**Q: What are the two required parts of any recursive function? What happens if you forget the base case, and what specific JavaScript error will you see?**

*Answer out loud before checking anything — name the base case and recursive case explicitly, and mention `RangeError: Maximum call stack size exceeded`.*

---

## 💻 WRITE WITHOUT AI

1. Write `factorial(n)` recursively. Test with `5` (expect `120`) and `1` (expect `1`, your base case).
2. Write `sumUpTo(n)` recursively — returns the sum of all integers from `1` to `n`. Test with `5` (expect `15`). Get the `return` right this time (see the debugging exercise above for what happens if you don't).
3. Write `countdown(n)` that logs each number from `n` down to `1`, then logs `"Liftoff!"` — no `return` value needed, just console logs, but still needs a clear base case.
4. Draw the call stack **by hand on paper** for `factorial(4)` — write out each call being pushed, then each return value as calls pop off, before running any code. Then verify your prediction by running it.
5. (Harder) Write `power(base, exp)` recursively (without using `**` or `Math.pow`) that computes `base` raised to `exp`. Think carefully about your base case (what should `power(base, 0)` return, and why?).

*(Reply with your attempts or where you get stuck before I give hints or the reference solution).*
