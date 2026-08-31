# PHASE 5 — EVENT HANDLING

## Topic 24: Change Events

### WHY
Not everything needs to respond on every single keystroke or click. Sometimes you specifically want to know **when a value has been finalized/changed** — a dropdown selection, a checkbox toggled, a file picked, or a text field losing focus after being edited. `change` (and its close relative `input`) events cover this.

### WHAT
The `change` event fires when an element's value has been modified **and committed** — typically when the element loses focus after its value changed (for text inputs), or immediately upon selection (for checkboxes, radio buttons, and `<select>` dropdowns). The related `input` event fires immediately on every value change, including every keystroke — useful to know as a contrast even though it's not separately listed on your syllabus by that name.

### THE BIG PICTURE FIRST

```text
User interacts with a form control (dropdown, checkbox, text field)
   ↓
Value changes
   ↓
For checkboxes/radio/select: "change" fires immediately on selection
For text inputs: "change" fires when the field loses focus, if the value changed
   ↓
Callback function runs
   ↓
Application logic responds
```

### MENTAL MODEL

Think of `change` as answering: **"did this value get committed to a new state?"** — as opposed to `click` ("something was clicked") or `keydown` ("a key was pressed"). It's about the *value* itself settling into a new state, not the raw interaction that caused it.

### SYNTAX

```javascript
// Checkbox
const checkbox = document.querySelector("#agree");
checkbox.addEventListener("change", (event) => {
    console.log(event.target.checked); // true or false
});

// Dropdown / <select>
const select = document.querySelector("#colorSelect");
select.addEventListener("change", (event) => {
    console.log(event.target.value); // the newly selected option's value
});

// Text input — fires on blur (losing focus) after a change, NOT on every keystroke
const textInput = document.querySelector("#name");
textInput.addEventListener("change", (event) => {
    console.log("Final value:", event.target.value);
});
```

### SMALLEST POSSIBLE EXAMPLE

```javascript
document.querySelector("#colorSelect").addEventListener("change", (event) => {
    console.log(event.target.value);
});
```

### MANUAL IMPLEMENTATION
Not a primitive to build — same browser-API-pattern family as the previous event topics. The manual skill: testing the **timing difference** yourself between `change` and typing — type into a text input with a `change` listener attached and confirm nothing logs until you click away, versus attaching to `keydown`/`input` and seeing it fire on every character.

### PRACTICAL USE
- Reacting to a dropdown selection (e.g., changing displayed content based on a category filter)
- Reacting to a checkbox toggle (e.g., "remember me," "show completed todos only" — directly relevant to your todo app)
- Validating a text field once the user finishes editing it and moves on, rather than validating on every keystroke (less jarring UX)
- File input changes (`<input type="file">` — detecting when a user picks a file)

### EDGE CASES

1. **`change` on text inputs does NOT fire on every keystroke** — this trips up people expecting live/reactive behavior:
```javascript
input.addEventListener("change", (event) => {
    console.log(event.target.value); // only logs after the field loses focus, not per keystroke
});
```
If you want live updates as the user types, you'd want the `input` event instead (not formally on your syllabus list, but directly relevant context, flagging it here since the contrast matters) —

> [!NOTE]
> **EXTENSION — NOT PART OF THE PROVIDED TOPICS**: the `input` event fires on every value change immediately (including every keystroke), unlike `change`. Not officially in your syllabus's Phase 5 list, but I'm noting it because confusing `change` with "live updates" is one of the most common mistakes with this topic, and you'll likely want `input` eventually for things like live character counters.

2. **Checkboxes: use `.checked`, not `.value`**, to get the actual boolean state:
```javascript
checkbox.addEventListener("change", (event) => {
    console.log(event.target.value);   // usually "on" (unhelpful default) or a custom value attribute
    console.log(event.target.checked); // true/false — this is what you actually want
});
```

3. **`change` on a text field only fires if the value is actually different from what it was when the field gained focus** — clicking into a field, typing nothing new, then clicking away does NOT fire `change`:
```javascript
// user clicks into input, doesn't type anything, clicks away — no "change" event fires
```

4. **`<select>` change events fire the moment a new option is chosen** — no blur/focus-loss requirement, unlike text inputs — this inconsistency across element types is a genuine source of confusion worth being aware of.

### COMMON MISTAKES
- Using `change` when you actually wanted live, per-keystroke updates (should use `input` instead — extension topic above)
- Reading `.value` on a checkbox instead of `.checked`
- Assuming `change` fires immediately for text inputs, then being confused when nothing logs until the field loses focus
- Forgetting that `change` requires the value to have actually changed — not just "the field was interacted with"

### DEBUGGING EXERCISE

```html
<input type="checkbox" id="showCompleted" />
<label for="showCompleted">Show completed only</label>
<script>
  const checkbox = document.querySelector("#showCompleted");

  checkbox.addEventListener("change", (event) => {
    if (event.target.value) {
      console.log("Showing completed only");
    } else {
      console.log("Showing all");
    }
  });
</script>
```

> [!NOTE]
> *(Reason through it: syntax error, runtime error, or logical error? What actually happens when the checkbox is checked and unchecked?)*
> 
> **Answer:** No error, but a logical bug. `event.target.value` on a checkbox without a custom `value` attribute defaults to the string `"on"` — which is a **non-empty string, and therefore always truthy**, regardless of whether the checkbox is checked or unchecked. So `if (event.target.value)` is always `true`, and the code logs `"Showing completed only"` every single time the checkbox changes, even when unchecking it. The fix is checking `event.target.checked` (the actual boolean state) instead of `event.target.value`.

## WHERE THIS APPEARS IN REAL SOFTWARE
- Filter/sort dropdowns on e-commerce or listing pages (`change` on a `<select>` re-filters displayed results)
- "Select all" / "show completed" checkboxes in todo/task apps — directly relevant to extending your Phase 4/5 todo project
- Settings toggles (dark mode, notification preferences) that persist immediately on change (often paired with `localStorage` from Topic 10)
- Form field validation triggered once a user finishes a field, rather than intrusively on every keystroke

> **WHERE YOU WILL SEE THIS LATER:**
> ```text
> change event on <select>/checkbox
>    ↓
> React's onChange={handler} — note: React's onChange actually behaves more like the native "input" event (fires on every keystroke), which is a well-known naming quirk worth remembering once you get there
> ```

### INTERVIEW QUESTION

**Q: Why does `event.target.value` return `"on"` for a plain checkbox, and what should you check instead to get its actual state? Also: why doesn't `change` fire on every keystroke for a text input?**

*Answer out loud before checking anything — cover `.checked` for checkboxes, and the focus-loss timing behavior for text input `change` events.*

---

## 💻 WRITE WITHOUT AI

1. Create a `<select>` with 3 `<option>` values (e.g., colors). Attach a `change` listener that logs the newly selected value each time.
2. Create a checkbox with a label. Attach a `change` listener that logs `"Checked"` or `"Unchecked"` based on `.checked` (not `.value`).
3. Create a text `<input>` with a `change` listener. Type into it without clicking away, and confirm nothing logs yet — then click away (or Tab out) and confirm it logs exactly once. Write a comment noting the exact moment it fired.
4. Add a second listener to the same text input using the `input` event (extension topic above) that logs on every keystroke, and directly compare its firing behavior against your `change` listener side-by-side to see the timing difference concretely.
5. (Harder) Extend your todo app: add a checkbox labeled "Show completed only" above your todo list. Attach a `change` listener that, based on `.checked`, filters what `renderTodos()` displays (show all todos vs. only `done: true` ones) — without changing the underlying `todos` array itself, only what's rendered.

*(Reply with what you built/observed, or where you get stuck, before I give hints or a reference solution).*
