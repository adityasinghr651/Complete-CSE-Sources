# Topic 1: Variables

## WHY
Every program needs to hold onto values while it runs (like a username or score). Variables give you a **named reference to a value stored in memory**, so your code can read, reuse, and update that value.

## WHAT
A variable is a label that points to a value. In JavaScript, you declare variables using `let`, `const`, or `var`.

- `let` — value can be reassigned later.
- `const` — value cannot be reassigned (the binding is constant).
- `var` — the old way (function-scoped, has quirks). Avoid using it.

## MENTAL MODEL
Think of a variable as a **labeled box**:

```javascript
let score = 10;
//   score
//  ┌─────┐
//  │ 10  │
//  └─────┘
```

Reassigning with `let` replaces what's in the box:

```javascript
score = 20;
//   score
//  ┌─────┐
//  │ 20  │
//  └─────┘
```

`const` is a box that's **sealed** after you put something in it — you can't swap the contents.
*(Note: for **objects/arrays**, `const` seals the box, not the contents. We'll cover this in Phase 2).*

## SYNTAX

```javascript
let age = 21;
const name = "Aditya";
var oldStyle = "avoid this";
```

## SMALLEST POSSIBLE EXAMPLE

```javascript
let x = 5;
console.log(x); // 5

x = 8;
console.log(x); // 8
```

## PRACTICAL USE
- Storing user input from a form.
- Storing a running total in a loop.
- Holding a flag like `isLoggedIn`.
- Holding a reference to a fetched API response.

## EDGE CASES
1. **Redeclaring `let` in the same scope** throws an error:
```javascript
let x = 1;
let x = 2; // SyntaxError: Identifier 'x' has already been declared
```
2. **Using a variable before declaration** (`let`/`const`) throws a `ReferenceError` due to the "temporal dead zone":
```javascript
console.log(y); // ReferenceError
let y = 5;
```
3. `var` gets hoisted and initialized as `undefined` (hides bugs):
```javascript
console.log(z); // undefined (not an error!)
var z = 5;
```

## COMMON MISTAKES
- Using `var` out of habit instead of `let`/`const`.
- Trying to reassign a `const`:
```javascript
const pi = 3.14;
pi = 3.14159; // TypeError: Assignment to constant variable
```
- Assuming `const` makes objects completely immutable.

## DEBUGGING EXERCISE
What's wrong here?

```javascript
function greet() {
    console.log(message);
    const message = "Hello";
}
greet();
```
> [!NOTE]
> **Answer:** `ReferenceError` — `const message` is hoisted but stays in the "temporal dead zone" until its declaration line executes. Accessing it before that line throws an error.

## WHERE THIS APPEARS IN REAL SOFTWARE
- Config values that shouldn't change → `const`
- Counters, accumulators, loop variables that change → `let`
- Modern codebases/linters (ESLint) actively forbid `var`.

> **WHERE YOU WILL SEE THIS LATER:** React component state, Node.js config objects, and function parameters all build directly on `let`/`const` semantics.

## INTERVIEW QUESTION
**Q: What's the difference between `var`, `let`, and `const`? Why would a team prefer `let`/`const`?**
*Practice answering this out loud covering scope (function vs block), hoisting behavior, and reassignment rules.*

---

## 💻 WRITE WITHOUT AI

Do these yourself, no AI, no copying:

1. Declare a `const` for your name and a `let` for your age. Print both using `console.log`.
2. Write code that increases a `let count` variable by 1, three times in a row, printing it each time.
3. Try to reassign a `const` and observe the exact error message in your console — write down what it says.
4. Predict, then verify: what happens if you declare `let total;` (no value) and then `console.log(total);`? What does it print, and why is this different from the `const` temporal-dead-zone case?
5. (Slightly harder) Write a snippet that demonstrates hoisting with `var` — i.e., logs `undefined` before the `var` line runs — and explain in a comment why this happens.
