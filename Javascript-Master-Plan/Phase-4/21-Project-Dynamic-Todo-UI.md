# PHASE 4 PROJECT — Dynamic Todo UI

This is your first project with a **real visible interface**. Still no event listeners yet (that's Phase 5) — so "adding" todos here will be triggered by calling functions directly from the console, not by clicking a button. That wiring comes next phase.

## REQUIREMENTS

Build a todo list UI where:
1. Todos are stored as an array of objects in JavaScript (`{ text, done }`).
2. The array is the **source of truth** — the visible list is always built *from* the array, not edited directly.
3. Adding a todo (via a function call) updates the array, then re-renders the list on the page.
4. Removing a todo (via a function call, referencing an id/index) updates the array, then re-renders.
5. Toggling a todo's `done` status (via a function call) updates the array, then re-renders, with some visual distinction for completed items (e.g., a class name you could style with CSS, even if you don't write CSS yet).

## FEATURES

- `todos` — array of `{ id, text, done }` objects (module-level state)
- `renderTodos()` — clears the `<ul>` and rebuilds it entirely from the current `todos` array, using `createElement` (Topic 20) for each item — this is the core "render from state" function
- `addTodo(text)` — creates a new todo object, pushes it into `todos`, calls `renderTodos()`
- `removeTodo(id)` — filters (or manually loops and rebuilds) `todos` to exclude the matching id, calls `renderTodos()`
- `toggleTodo(id)` — finds the matching todo, flips its `done` boolean, calls `renderTodos()`

## EXPECTED BEHAVIOR

```html
<ul id="todoList"></ul>
```

```javascript
addTodo("Buy milk");
addTodo("Walk dog");
addTodo("Write JS notes");
// page now shows 3 <li> elements

toggleTodo(2); // "Walk dog" now shown with a "done" class/style
removeTodo(1); // "Buy milk" removed entirely, other 2 remain, re-rendered
```

*(Visually, after these calls, the list should show 2 remaining items, with "Walk dog" visually marked done in some way (even something as simple as a `done` class name applied — actual styling is optional since CSS isn't your syllabus focus here).)*

## SUGGESTED FILE STRUCTURE

```text
phase4-todo-ui/
  index.html
  todo.js
```

`index.html` needs a `<ul id="todoList"></ul>` and a `<script src="todo.js"></script>` at the end of `<body>` (remember Topic 17's lesson about script placement).

## CONCEPTS BEING TESTED

- `querySelector` (selecting the `<ul>` once, keeping a reference)
- `createElement` + `appendChild` (building each `<li>` — NOT `innerHTML +=`, this project is specifically meant to reinforce the "proper" dynamic creation pattern over the string-based one)
- Arrays of objects (Phase 2) — `todos` state, filtering/looping to find/remove/toggle
- The **"render from state"** pattern: the DOM is never edited directly by add/remove/toggle — they only change the `todos` array, then call `renderTodos()` to rebuild the visible list from scratch. This is the single most important idea in this project, and it's the direct conceptual seed of how React works.
- Functions, loops, conditionals (Phase 1) — used throughout

## IMPLEMENTATION MILESTONES

1. Set up `index.html` with an empty `<ul id="todoList">` and link `todo.js` correctly (end of body).
2. Create the `todos` array (start empty) and write `renderTodos()` — for now, just make it clear the `<ul>` and rebuild `<li>` elements from whatever's currently in `todos` (test by manually pre-filling `todos` with 2 hardcoded objects and calling `renderTodos()` once).
3. Write `addTodo(text)` — decide how you'll generate unique `id`s (a simple incrementing counter is fine, don't overthink this). Push the new todo, call `renderTodos()`.
4. Write `removeTodo(id)` — filter out the matching todo by id, call `renderTodos()`.
5. Write `toggleTodo(id)` — find the todo by id, flip `.done`, call `renderTodos()`.
6. In `renderTodos()`, add a visual marker for `done` todos — e.g., `li.classList.add("done")` if `todo.done` is true, or simply prefix the text with `"[DONE] "` if you'd rather not deal with classes/CSS at all right now — your choice, just be consistent.
7. Test the full flow from the console: add several todos, toggle one, remove one, confirm the page updates correctly each time and the array stays the correct source of truth.

## MANUAL TEST CASES

| Action | Expected Result |
|---|---|
| `addTodo("Buy milk")` | 1 new `<li>` appears with that text |
| `addTodo("Walk dog")`, `addTodo("Write notes")` | 3 `<li>` items total, in the order added |
| `toggleTodo(<id of "Walk dog">)` | that specific item shows a "done" marker; others unaffected |
| `toggleTodo(<same id again>)` | toggles back to not-done (confirm toggle, not just "mark done") |
| `removeTodo(<id of "Buy milk">)` | that item disappears; the other 2 remain, in original relative order |
| `removeTodo(<id that doesn't exist>)` | no crash; list unchanged |
| Calling `renderTodos()` directly with no changes | list looks identical (rebuilding from the same state produces the same visible result) |

## EDGE CASES TO HANDLE

- Removing a todo by an id that doesn't exist — shouldn't crash or accidentally remove something else
- Toggling a todo by an id that doesn't exist — should just do nothing (or log a message), not throw
- Adding an empty-string todo (`addTodo("")`) — decide whether to allow this or silently ignore it; either is acceptable here as long as it's a deliberate choice (real validation comes in Phase 11)
- Calling `renderTodos()` before any todos are added — should just show an empty `<ul>`, no crash

## 💻 WHAT YOU SHOULD IMPLEMENT YOURSELF

Everything — no starter code. In particular, think carefully about:
- **Why `renderTodos()` clears and rebuilds the entire list every time**, rather than trying to surgically update just the one `<li>` that changed. This is intentionally simple/wasteful compared to what a production app would do — but it's the clearest way to understand the "state → render" mental model before you meet React, which handles the "only update what changed" optimization for you automatically.
- How you clear the `<ul>` before rebuilding — you have a couple of options here (setting `innerHTML = ""` is one valid, simple choice even though Topic 20 emphasized avoiding `innerHTML` for *building* content — clearing via `innerHTML = ""` is a common, accepted exception since there's nothing being parsed, just erased. Alternatively, loop and `.remove()` each child manually if you want more practice with that pattern instead).
- Keeping `todos` as the single source of truth — resist any urge to read current state back out of the DOM; every function should operate on the `todos` array only.

> [!TIP]
> Build it, test against the table above, and share your code + what you observe when done or stuck — I'll review it, not rewrite it, per your rules.
