# Topic 3: Scope

## WHY
As programs grow, you end up with many variables. If every variable were visible everywhere, you'd get name collisions, accidental overwrites, and code that's impossible to reason about. Scope is the rule system that determines **where a variable can be accessed** — it's what lets you safely reuse names like `i` or `result` in different parts of your code without them interfering with each other.

## WHAT
Scope is the region of code where a variable is accessible. JavaScript has:

- **Global scope** — accessible everywhere in the file
- **Function scope** — variables declared inside a function are only accessible inside that function
- **Block scope** — variables declared with `let`/`const` inside `{ }` (an `if`, `for`, or any block) are only accessible inside that block

`var` ignores block scope (only respects function scope) — this is one of the main reasons `let`/`const` replaced it.

## MENTAL MODEL

Think of scope as **nested rooms**. A variable declared in an inner room is invisible from an outer room, but code in an inner room can see variables from the rooms surrounding it.

```text
┌─────────────────────────────┐
│ Global scope                │
│   let globalVar             │
│                             │
│  ┌────────────────────────┐ │
│  │ function scope         │ │
│  │   let functionVar      │ │
│  │                        │ │
│  │  ┌────────────────────┐│ │
│  │  │ block scope (if/for││ │
│  │  │   let blockVar     ││ │
│  │  └────────────────────┘│ │
│  └────────────────────────┘ │
└─────────────────────────────┘
```

Code inside the innermost room can see `globalVar`, `functionVar`, and `blockVar`. Code in the global room can only see `globalVar`.

## SYNTAX

```javascript
let globalVar = "I'm global";

function outer() {
    let functionVar = "I'm function-scoped";

    if (true) {
        let blockVar = "I'm block-scoped";
        console.log(globalVar);   // accessible
        console.log(functionVar); // accessible
        console.log(blockVar);    // accessible
    }

    console.log(blockVar); // ReferenceError — blockVar doesn't exist here
}
```

## SMALLEST POSSIBLE EXAMPLE

```javascript
if (true) {
    let x = 10;
}
console.log(x); // ReferenceError: x is not defined
```

Compare with `var`:

```javascript
if (true) {
    var y = 10;
}
console.log(y); // 10 — var leaks out of the block
```

## PRACTICAL USE
- Keeping loop counters (`for (let i = 0...)`) isolated per iteration so closures capture the right value.
- Keeping helper variables inside a function so they don't pollute global scope and collide with other code (especially important when combining multiple scripts/libraries).
- Encapsulating temporary state inside `if`/`for` blocks.

## EDGE CASES

1. **`var` in a loop leaks and gets reused across iterations** — classic interview gotcha:
```javascript
for (var i = 0; i < 3; i++) {
    setTimeout(() => console.log(i), 0);
}
// prints 3, 3, 3 — not 0, 1, 2
```
`let` fixes this because each loop iteration gets its own binding:
```javascript
for (let i = 0; i < 3; i++) {
    setTimeout(() => console.log(i), 0);
}
// prints 0, 1, 2
```

2. **Shadowing** — an inner-scope variable with the same name as an outer one "hides" it inside that block:
```javascript
let value = "outer";
if (true) {
    let value = "inner";
    console.log(value); // "inner"
}
console.log(value); // "outer" — unaffected
```

3. **Global variables created without declaration** (no `let`/`const`/`var`) — this is an easy way to accidentally create bugs:
```javascript
function leak() {
    accidentalGlobal = "oops"; // no declaration keyword
}
leak();
console.log(accidentalGlobal); // "oops" — leaked into global scope
```

## COMMON MISTAKES
- Assuming `{ }` always creates a new scope for `var` (it doesn't — only functions do for `var`).
- Relying on variable leakage from `var` in loops, causing the classic "prints 3,3,3" bug.
- Forgetting to declare a variable and accidentally creating a global.
- Not realizing that nested functions can read outer variables (this is the seed of **closures**, which comes later — you don't need it yet, just notice the "inner-can-see-outer" direction).

## DEBUGGING EXERCISE

```javascript
function processOrders() {
    for (var i = 0; i < 3; i++) {
        // pretend this schedules something async
        setTimeout(function () {
            console.log("Processing order " + i);
        }, 100);
    }
}
processOrders();
```

> [!NOTE]
> *(Before reading on: what does this print, and why? What single-word change fixes it?)*
> 
> **Answer:** Prints `"Processing order 3"` three times. Because `var` is function-scoped, all three `setTimeout` callbacks share the *same* `i`, and by the time they run (after the loop finishes), `i` is `3`. Changing `var` to `let` fixes it, because `let` creates a fresh `i` binding per iteration.

## WHERE THIS APPEARS IN REAL SOFTWARE
- Every module/file in a real codebase relies on scope to avoid variable name collisions between files
- The `var`-in-loop bug above is a real, commonly-cited interview and debugging trap — it shows up whenever people write async code (timers, API calls) inside loops
- Linters (ESLint's `no-var`, `block-scoped-var`) exist specifically to catch scope-related bugs before they ship

> **WHERE YOU WILL SEE THIS LATER:** React hooks and closures rely heavily on correct block scoping; Node.js modules use function/module scope to keep internal variables private.

## INTERVIEW QUESTION

**Q: What's the difference between function scope and block scope? Give an example of a bug that happens if you use `var` instead of `let` inside a loop.**

*Answer out loud, including the `setTimeout` example above from memory (not by copying) — that's a very common real interview question.*

---

## 💻 WRITE WITHOUT AI

1. Write a function with an `if` block inside it. Declare a `let` variable inside the `if` block and try to log it outside the block. Confirm you get a `ReferenceError`.
2. Repeat exercise 1 but with `var` instead of `let`. Confirm it does NOT throw, and explain in a comment why.
3. Write a `for` loop using `let i`, and inside the loop body, push `i` into an array via a function called after a delay (use `setTimeout(() => {...}, 0)`). Confirm you get `0, 1, 2` printed.
4. Change exercise 3 to use `var i` instead, and confirm you get `3, 3, 3` (or whatever the loop's final value is). Write a comment explaining why the outputs differ.
5. (Harder) Write two functions, `outer` and `inner`, where `inner` is defined **inside** `outer`. Declare a variable in `outer` and log it from inside `inner` to confirm inner scopes can see outer variables. Don't call this a "closure" formally yet — just observe the behavior.

*(Reply with your results or where you get stuck — I'll give hints before any reference solution).*
