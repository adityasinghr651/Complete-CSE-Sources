# PHASE 5 PROJECT — Interactive Form

This project combines everything from Phase 5 (click, keyboard, form submit, change events) with your full todo app foundation from Phase 4, plus everything underneath. This is the point where your todo app becomes a genuinely interactive, self-contained mini-application.

## REQUIREMENTS

Upgrade your Phase 4 Todo UI into a fully interactive form-driven app:

1. Replace direct console function calls with a real `<form>` for adding todos.
2. Each todo item in the list gets a **delete button** and a **toggle checkbox/click** — both wired to real event listeners, not console calls.
3. Add a "Show completed only" filter checkbox that changes what's *displayed* without changing the underlying data.
4. Support adding a todo via both clicking "Add" and pressing Enter (the form submit event naturally covers both — confirm you understand why, from Topic 23).
5. Prevent adding empty/whitespace-only todos (a light validation preview — full validation rigor comes in Phase 11).

## FEATURES

- `<form id="todoForm">` wrapping a text `<input id="todoInput">` and a submit `<button>`
- `form.addEventListener("submit", ...)` — prevents default, reads and trims the input value, ignores empty submissions, calls `addTodo(text)`, clears the input
- Each rendered `<li>` (inside `renderTodos()`) now includes:
  - A checkbox (or the `<li>` itself is clickable) wired to `toggleTodo(id)`
  - A delete `<button>` wired to `removeTodo(id)`
- `<input type="checkbox" id="showCompletedOnly">` with a `change` listener that toggles a filter flag and calls `renderTodos()`
- `renderTodos()` now checks the filter flag and only builds `<li>` elements for todos that should currently be visible

## EXPECTED BEHAVIOR

- Typing "Buy milk" and pressing Enter (or clicking Add) → new todo appears, input clears
- Typing only spaces and submitting → nothing is added, input clears or stays (your choice), no crash
- Clicking a todo's checkbox → that item visually marks done/undone, list re-renders
- Clicking a todo's delete button → that item disappears, others remain, list re-renders
- Checking "Show completed only" → list re-renders showing only done items
- Unchecking it → list re-renders showing all items again
- Refreshing the page → todos do NOT persist (that's intentionally out of scope here — Topic 10's localStorage could be added as a stretch goal, but isn't required by this project)

## SUGGESTED FILE STRUCTURE

```text
phase5-interactive-todo/
  index.html
  todo.js
```

`index.html` needs: a `<form id="todoForm">` with an `<input id="todoInput">` and submit button, a `<label>`+`<input type="checkbox" id="showCompletedOnly">`, and a `<ul id="todoList">` — all before the `<script src="todo.js">` tag at the end of `<body>`.

## CONCEPTS BEING TESTED

- Form submit events + `preventDefault()` (Topic 23)
- Click listeners on dynamically created elements (Topic 21) — specifically, attaching a listener to each delete button and checkbox **at creation time**, inside `renderTodos()`, since those elements don't exist until the render happens
- Change events on a checkbox (Topic 24)
- The render-from-state pattern from Phase 4, now with a **filter** layered on top of the raw `todos` array — an important new idea: the array stays complete/unfiltered, only the *rendering* is filtered
- String methods preview: `.trim()` on the input value (formally covered in Phase 10, but a small, safe preview here for basic empty-input handling)
- Arrays/objects, functions, loops, conditionals — everything underneath, as always

## IMPLEMENTATION MILESTONES

1. Update `index.html`: wrap the todo input in a real `<form>`, add the "Show completed only" checkbox with its label, keep the `<ul>`.
2. Update the form handling: remove any old keydown-based Enter handling from Topic 22 (if you built that) in favor of the form's native `submit` event — confirm both click and Enter still work through this single listener.
3. Add input validation: trim the value, and if it's empty after trimming, don't call `addTodo()` at all.
4. Modify `renderTodos()` so that for each todo, it also creates a checkbox (reflecting `todo.done` via `.checked`) and a delete button, each with their own `addEventListener` calls wired to `toggleTodo(id)` / `removeTodo(id)` respectively — attached fresh, every render, since the elements themselves are recreated every render.
5. Add a `showCompletedOnly` boolean variable (module-level state, alongside `todos`). Attach a `change` listener on the filter checkbox that flips this variable and calls `renderTodos()`.
6. Update `renderTodos()`'s core loop to skip todos that don't match the current filter state (i.e., if `showCompletedOnly` is true, only build `<li>` elements for todos where `done === true`).
7. Test the full interactive flow end-to-end: add several todos (including trying to add an empty one), toggle some, delete some, and flip the filter checkbox — confirm the array (`todos`) and the visible list stay correctly in sync at every step.

## MANUAL TEST CASES

| Action | Expected Result |
|---|---|
| Type "Buy milk", press Enter | Todo added, input cleared, appears in list |
| Type "Walk dog", click "Add" button | Same result as pressing Enter — todo added |
| Submit with empty input | Nothing added, no crash |
| Submit with only spaces ("   ") | Nothing added (after `.trim()`), no crash |
| Click a todo's checkbox | That item toggles done/undone visually; `todos` array's `done` field updates correctly |
| Click a todo's delete button | That item removed from both the array and the visible list |
| Check "Show completed only" with some todos done | Only done todos shown |
| Uncheck it again | All todos shown again, in original order |
| Toggle a todo while filter is active, so it no longer matches the filter | It disappears from view immediately (since it no longer matches, even though it's still in `todos`) |

## EDGE CASES TO HANDLE

- Deleting the last remaining todo — list should render empty, no crash
- Toggling/deleting via buttons that reference an id that's somehow already been removed (shouldn't happen in normal use, but your `toggleTodo`/`removeTodo` from Phase 4 should already handle unknown ids gracefully — confirm this still holds)
- Rapidly submitting several todos in a row — each should be added correctly, in order, without duplicate ids
- Toggling the filter checkbox when there are zero todos at all — should just show an empty list, no crash

## WHAT YOU SHOULD IMPLEMENT YOURSELF

Everything — no starter code. Pay particular attention to:
- **Where exactly you attach the checkbox/delete-button listeners** — they must be attached *inside* `renderTodos()`, on each newly created element, every single time it re-renders (since old elements are destroyed and replaced each render, any listeners on the old ones are irrelevant garbage now — new elements need fresh listeners every time). This is a direct, concrete consequence of the "destroy and rebuild" render pattern from Phase 4, now colliding with Phase 5's events for the first time — a genuinely important realization.
- Keeping `todos` (the real data) and the **filtered view** (what's currently rendered) conceptually separate — never filter by mutating or removing from `todos` itself, only filter what you choose to build `<li>` elements for.
- Deciding where `.trim()` happens — on the input's raw value before deciding whether to call `addTodo`, not inside `addTodo` itself (though either placement is defensible — just be intentional and consistent).

Build it, test against the table above, and share your code + what you observe when done or stuck — I'll review it, not rewrite it, per your rules.

---

That's the full Phase 5 project — your todo app is now a genuinely interactive mini-application built entirely from first principles. This is a strong checkpoint to pause and make sure everything from Phases 1–5 feels solid before moving into OOP.
