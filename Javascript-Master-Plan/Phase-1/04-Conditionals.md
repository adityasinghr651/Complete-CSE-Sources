# Topic 4: Conditionals

## WHY
Programs need to make decisions. A login form needs to check if credentials are valid before letting a user in. A game needs to check if a player has collided with an obstacle. Without conditionals, code would always run the exact same way regardless of the situation — completely useless for real applications.

## WHAT
Conditionals let your code branch: run one block of code if something is true, another if it's false (or check several possibilities in sequence). JavaScript gives you `if/else if/else`, the ternary operator, and `switch`.

## MENTAL MODEL

Think of it as a **fork in a road**, where the direction taken depends on a condition:

```text
        condition?
       /          \
     true         false
      │             │
      ▼             ▼
  do this      do that instead
```

For multiple conditions, it's a chain of forks, checked top to bottom — the first one that's `true` wins, and the rest are skipped.

## SYNTAX

```javascript
if (condition) {
    // runs if condition is truthy
} else if (otherCondition) {
    // runs if condition is falsy AND otherCondition is truthy
} else {
    // runs if none matched
}

// Ternary — a compact if/else that returns a value
const status = age >= 18 ? "adult" : "minor";

// switch — good for many exact-value checks
switch (day) {
    case "Mon":
        console.log("Start of week");
        break;
    case "Fri":
        console.log("Almost weekend");
        break;
    default:
        console.log("Regular day");
}
```

## SMALLEST POSSIBLE EXAMPLE

```javascript
const age = 20;
if (age >= 18) {
    console.log("Adult");
} else {
    console.log("Minor");
}
// "Adult"
```

## MANUAL IMPLEMENTATION
Not a "build from scratch" primitive, but the important manual skill is **tracing truthy/falsy values by hand** — see Edge Cases below. Given a value, you should be able to say instantly whether `if (value)` runs or not, without running the code.

## PRACTICAL USE
- Validating form fields before submission
- Showing different UI based on login state (`isLoggedIn ? <Dashboard/> : <LoginForm/>` — you'll see this exact pattern in React later)
- Branching API response handling (`if (response.ok) {...} else {...}`)
- Game logic (`if (player.health <= 0) { gameOver(); }`)

## EDGE CASES

1. **Truthy/falsy values** — `if` doesn't require a strict boolean; it coerces the value. These are ALL falsy:
```javascript
if (false) {}
if (0) {}
if ("") {}
if (null) {}
if (undefined) {}
if (NaN) {}
```
Everything else is truthy — including surprising ones:
```javascript
if ("0") {}       // truthy! (non-empty string)
if ([]) {}        // truthy! (empty array is still an object)
if ({}) {}        // truthy!
```

2. **`=` vs `==` vs `===`** — a classic beginner trap:
```javascript
let x = 5;
if (x = 10) { // assignment, not comparison! Always truthy (10 is truthy)
    console.log("oops");
}
```
Always use `===` (strict equality — no type coercion) unless you have a specific reason to use `==`.

3. **`==` coerces types in sometimes-surprising ways**:
```javascript
console.log(0 == "0");           // true  (coerced)
console.log(0 === "0");          // false (no coercion, different types)
console.log(null == undefined);  // true
console.log(null === undefined); // false
```

4. **Missing `break` in `switch` falls through** to the next case:
```javascript
switch (2) {
    case 1:
    case 2:
        console.log("one or two"); // runs
    case 3:
        console.log("three");      // ALSO runs — no break above it
        break;
    default:
        console.log("other");
}
```

## COMMON MISTAKES
- Using `==` instead of `===` and getting bitten by coercion.
- Using `=` instead of `==`/`===` inside a condition.
- Forgetting `break` in `switch` statements.
- Writing deeply nested `if/else` chains instead of using early returns (a code-quality issue we'll revisit once you're writing bigger functions).
- Treating falsy values inconsistently — e.g., checking `if (count)` when `count` could legitimately be `0` (a valid value, but falsy).

## DEBUGGING EXERCISE

```javascript
function checkStock(quantity) {
    if (quantity = 0) {
        return "Out of stock";
    } else {
        return "In stock";
    }
}

console.log(checkStock(0));  // ?
console.log(checkStock(5));  // ?
```

> [!NOTE]
> *(Reason it through: syntax error, runtime error, or logical error? What actually gets printed for both calls, and why?)*
> 
> **Answer:** No error — this runs. `quantity = 0` is an **assignment**, not a comparison. It assigns `0` to `quantity`, and the expression evaluates to `0`, which is falsy — so the `if` branch never runs, and `"In stock"` is returned **every single time**, regardless of the input. `checkStock(0)` → `"In stock"` (wrong!), `checkStock(5)` → `"In stock"`. This is a logical error hiding in plain sight, and it's exactly why `===` is the default professional habit.

## WHERE THIS APPEARS IN REAL SOFTWARE
- Form validation logic (checking required fields, valid formats)
- Authorization checks (`if (user.role === "admin") {...}`)
- API response handling (branching on status codes)
- Feature flags (`if (featureEnabled) {...}`) — common in production systems to roll out features gradually

> **WHERE YOU WILL SEE THIS LATER:**
> ```text
> if/else conditionals
>    ↓
> Conditional rendering in React (ternaries, && operator in JSX)
>    ↓
> Route guards in Express (if user not authenticated → redirect)
> ```

## INTERVIEW QUESTION

**Q: What's the difference between `==` and `===`? Give an example where they produce different results, and explain why you'd default to `===` in production code.**

*Answer out loud before checking anything — use one of the coercion examples above from memory, don't just recite mine.*

---

## 💻 WRITE WITHOUT AI

1. Write a function `checkAge(age)` that returns `"minor"` if under 18, `"adult"` if 18–64, and `"senior"` if 65+. Use `if/else if/else`.
2. Rewrite exercise 1's logic using a ternary for just the minor/adult split (ignore senior for this version) — get comfortable with ternary syntax.
3. Predict, then verify: what does `if ([])` do — does the block run or not? Write your prediction as a comment BEFORE running it.
4. Write a `switch` statement that logs the number of days in a given month name (just handle 3–4 months, doesn't need to be exhaustive). Deliberately leave out one `break` and observe/explain the fall-through behavior.
5. (Harder) Write a function `isEqual(a, b)` that takes two values and logs both `a == b` and `a === b` results. Call it with at least 3 pairs where the two comparisons disagree (like `0` and `"0"`) — find these pairs yourself, don't just copy the ones above.

*(Reply with your attempts or where you're stuck before I give hints or the reference solution).*
