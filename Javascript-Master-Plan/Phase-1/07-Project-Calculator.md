# PHASE 1 PROJECT — Calculator / CLI-Style Mini Program

This is your first checkpoint: a small project combining everything from Phase 1 (variables, functions, scope, conditionals, loops, template literals) — nothing more. No arrays/objects yet (that's Phase 2), no DOM yet (that's Phase 4). This runs purely in the console via `console.log` and hardcoded/prompted inputs.

## REQUIREMENTS

Build a simple calculator that:
1. Takes two numbers and an operator (`+`, `-`, `*`, `/`).
2. Performs the correct operation based on the operator.
3. Prints a nicely formatted result using a template literal.
4. Handles invalid operators gracefully (doesn't crash, gives a clear message).
5. Handles division by zero gracefully.

## FEATURES

- A function `calculate(num1, operator, num2)` that returns the result or an error message.
- A function `displayResult(num1, operator, num2, result)` that logs a formatted message like:
  `"5 + 3 = 8"`
- A small loop that runs the calculator on a **list of test cases** (you'll hardcode an array-like sequence of calls — since arrays aren't "officially" covered yet, it's fine to just call `calculate` multiple times with different values; don't reach for real arrays yet even though you technically know the syntax).

## EXPECTED BEHAVIOR

```text
5 + 3 = 8
10 - 4 = 6
6 * 7 = 42
20 / 4 = 5
8 / 0 = Error: Cannot divide by zero
9 % 2 = Error: Unsupported operator
```

## SUGGESTED FILE STRUCTURE

Since this is plain JS with no build tooling yet, keep it to one file:

```text
phase1-calculator/
  calculator.js
```

## CONCEPTS BEING TESTED

- Functions (parameters, return values)
- Conditionals (`if/else if/else` or `switch` to branch on operator)
- Template literals (building the output string)
- Scope (keeping helper variables local to functions, not global)
- Loops (optional — only if you choose to structure your test runs as a loop over a sequence of calls rather than repeated function calls; either approach is fine at this stage)

## IMPLEMENTATION MILESTONES

Work in this order — don't jump ahead:

1. Write `calculate(num1, operator, num2)` that handles `+` and `-` only. Test manually with `console.log`.
2. Add `*` and `/` to the same function.
3. Add the divide-by-zero check — return a distinct error message, don't let it silently produce `Infinity`.
4. Add a fallback (`else`/`default`) for unsupported operators (e.g., `%`, `^`), returning a clear error message.
5. Write `displayResult(...)` that logs using a template literal, in the exact format shown above.
6. Run at least 6 test cases covering: normal `+`, normal `-`, normal `*`, normal `/`, division by zero, unsupported operator.

## MANUAL TEST CASES

| num1 | operator | num2 | expected output |
|------|----------|------|------------------|
| 5    | `+`      | 3    | `5 + 3 = 8` |
| 10   | `-`      | 4    | `10 - 4 = 6` |
| 6    | `*`      | 7    | `6 * 7 = 42` |
| 20   | `/`      | 4    | `20 / 4 = 5` |
| 8    | `/`      | 0    | `8 / 0 = Error: Cannot divide by zero` |
| 9    | `%`      | 2    | `9 % 2 = Error: Unsupported operator` |

## EDGE CASES TO HANDLE

- Division by zero (`num2 === 0` when operator is `/`)
- Unknown/unsupported operator
- Negative numbers as input (e.g., `-5 + 3`) — should still work correctly, don't special-case it incorrectly
- Decimal numbers (e.g., `2.5 * 4`) — make sure your operator logic doesn't assume integers

## 💻 WHAT YOU SHOULD IMPLEMENT YOURSELF

Everything. This is intentionally small — I'm not giving starter code. Your job:
- Decide `if/else if` vs `switch` for the operator branching (either is fine — but be ready to justify your choice if asked in an interview).
- Get the divide-by-zero check placed correctly (before or after the general `/` case — order matters).
- Get the template literal formatting exactly right, including how you display the error cases (should the format still show `"8 / 0 = ..."` or something else? Your call — just be consistent).

> [!TIP]
> Build it, run your test cases, and paste your code + output when you're done (or when you get stuck) — I'll review it, not hand you a fix outright, per your rules. I'll only show a **REFERENCE SOLUTION — OPEN ONLY AFTER ATTEMPTING** if you ask for it after a real attempt.
