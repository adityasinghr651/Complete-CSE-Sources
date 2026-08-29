# PHASE 5 — EVENT HANDLING

## Topic 22: Keyboard Events

### WHY
Not every interaction is a click. Users type in text fields, press Enter to submit forms, use arrow keys to navigate, or press Escape to close a modal. Keyboard events let JavaScript respond to individual key presses, which is essential for anything involving text input or keyboard-driven interaction.

### WHAT
Keyboard events (`keydown`, `keyup`, and the less commonly used `keypress`) fire when the user interacts with the keyboard while an element has focus. The event object passed to your handler tells you exactly which key was involved.

### THE BIG PICTURE FIRST

```text
User presses a key
   ↓
Browser detects keydown (and later keyup)
   ↓
Event listener on the focused element (often an <input>)
   ↓
Callback function runs, receives event object
   ↓
event.key tells you which key was pressed
   ↓
Application logic responds (e.g., submit on Enter)
```

### MENTAL MODEL

Think of keyboard events as **click listeners' cousin** — same `addEventListener` pattern, just a different event name and a differently-shaped event object (one that carries key information instead of click coordinates).

### SYNTAX

```javascript
const input = document.querySelector("#myInput");

input.addEventListener("keydown", (event) => {
    console.log(event.key); // e.g., "a", "Enter", "Backspace", "Shift"
});

// Common pattern: respond only to a specific key
input.addEventListener("keydown", (event) => {
    if (event.key === "Enter") {
        console.log("Enter was pressed!");
    }
});
```

- `keydown` — fires when a key is pressed down (fires repeatedly if held)
- `keyup` — fires when a key is released (fires once per press)
- `event.key` — the human-readable key value (`"Enter"`, `"a"`, `"Escape"`, `"ArrowUp"`)

### SMALLEST POSSIBLE EXAMPLE

```javascript
document.querySelector("input").addEventListener("keydown", (event) => {
    console.log(event.key);
});
```

### MANUAL IMPLEMENTATION
Not a primitive to build — same as click listeners, this is a browser API pattern. The manual skill: getting comfortable **reading `event.key` values by testing them yourself** — pressing different keys and logging what actually comes back, rather than guessing/assuming key names (e.g., is it `"Esc"` or `"Escape"`? Testing settles this immediately).

### PRACTICAL USE
- Submitting a form or todo item when the user presses Enter in an input field (directly relevant to finishing your Phase 4 todo app properly)
- Closing a modal when Escape is pressed
- Implementing keyboard shortcuts (e.g., `Ctrl+S` to save — checking `event.ctrlKey` alongside `event.key`)
- Live character counting or input filtering as the user types

### EDGE CASES

1. **`event.key` vs the older `event.keyCode`** — `keyCode` is a legacy numeric code (deprecated, but you may see it in older code/tutorials); `event.key` is the modern, readable standard and what you should use:
```javascript
console.log(event.key);     // "Enter" — readable, use this
console.log(event.keyCode); // 13 — legacy numeric code, avoid for new code
```

2. **`keydown` fires repeatedly while a key is held down**, which can cause a handler to run many times in quick succession if you're not careful (e.g., logging every repeat, or triggering an action multiple times unintentionally):
```javascript
// holding "a" down fires many keydown events, one per OS-level key-repeat interval
```

3. **Modifier keys have their own boolean properties on the event object**, useful for shortcuts:
```javascript
input.addEventListener("keydown", (event) => {
    if (event.ctrlKey && event.key === "s") {
        event.preventDefault(); // stop the browser's default "Save Page" dialog
        console.log("Custom save triggered");
    }
});
```
(`event.preventDefault()` is used again more heavily in Phase 11 — form submissions — but it's genuinely relevant here too, since browsers have default behaviors for many key combinations.)

4. **Keyboard events only fire on elements that can receive focus** (like `<input>`, `<textarea>`, or elements with `tabindex`) — attaching a keyboard listener to a `<div>` that was never focused (e.g., by clicking into it or tabbing to it) simply won't fire, which confuses people expecting it to behave like a global listener:
```javascript
document.querySelector("div").addEventListener("keydown", ...); 
// only fires if that div is focused first — easy to forget
```

> [!NOTE]
> Attaching to `document` itself is the common workaround for page-wide keyboard shortcuts, since `document` effectively "hears" key events as they propagate —
> 
> **EXTENSION — NOT PART OF THE PROVIDED TOPICS**: event propagation/bubbling in depth isn't on your syllabus list, but "attach to `document` for global keyboard shortcuts" is a practical pattern worth knowing exists; I won't go deep into bubbling mechanics unless you want that as an add-on.

### COMMON MISTAKES
- Checking `event.keyCode` in new code instead of the modern `event.key`
- Assuming a keyboard listener on a non-focusable element (like a plain `<div>` with nothing done to make it focusable) will just work
- Forgetting that `keydown` repeats while held, causing unintended repeated logic (e.g., accidentally submitting a form multiple times if Enter is held rather than tapped)
- Comparing `event.key` case-sensitively against the wrong casing (`"enter"` vs the correct `"Enter"`)

### DEBUGGING EXERCISE

```html
<input id="search" type="text" />
<script>
  const input = document.querySelector("#search");

  input.addEventListener("keydown", (event) => {
    if (event.key === "enter") {
      console.log("Searching...");
    }
  });
</script>
```

> [!NOTE]
> *(Reason through it: syntax error, runtime error, or logical error? What happens when the user presses Enter?)*
> 
> **Answer:** No error, but a logical bug: `event.key` for the Enter key is exactly `"Enter"` (capital E), not `"enter"`. The comparison `event.key === "enter"` is a case-sensitive string comparison that will **never** match, so `"Searching..."` never logs, no matter how many times Enter is pressed. This is a classic small-typo bug that's easy to miss because nothing crashes — it just silently never triggers. The fix is matching the exact casing: `event.key === "Enter"`.

## WHERE THIS APPEARS IN REAL SOFTWARE
- Search bars that trigger search on Enter, without needing a separate button click
- Chat applications (send message on Enter, newline on Shift+Enter)
- Keyboard shortcuts in productivity apps (command palettes, save shortcuts)
- Accessibility: many users navigate primarily via keyboard, so proper keyboard event handling isn't just a convenience feature — it's often a requirement for usable, accessible software

> **WHERE YOU WILL SEE THIS LATER:**
> ```text
> addEventListener("keydown", handler)
>    ↓
> React's onKeyDown={handler} prop (same underlying event object and event.key values)
> ```

### INTERVIEW QUESTION

**Q: What's the difference between `keydown` and `keyup`? Why might using `keydown` alone cause issues if a key is held down, and how does that relate to `event.key` vs the deprecated `event.keyCode`?**

*Answer out loud before checking anything — cover: `keydown` repeats while held, `keyup` fires once on release, and the modern `event.key` vs legacy `event.keyCode` distinction.*

---

## 💻 WRITE WITHOUT AI

1. Create an `<input>` and attach a `keydown` listener that logs `event.key` for every key pressed while the input is focused.
2. Modify it so that pressing specifically the `"Enter"` key logs `"Submitted!"` instead of (or in addition to) the general key log.
3. Add a check for `event.key === "Escape"` that clears the input's value (`input.value = ""`) when Escape is pressed.
4. Write a listener that checks for `event.ctrlKey && event.key === "Enter"` (a "Ctrl+Enter" combo) and logs a distinct message — test that plain Enter alone does NOT trigger this specific handler's message.
5. (Harder) Combine this with Phase 4's todo project: attach a `keydown` listener to a real `<input id="todoInput">`, and when `Enter` is pressed, read `input.value`, call your existing `addTodo(text)` function with it, then clear the input (`input.value = ""`). This is the real wiring your todo app has been missing.

*(Reply with what you built/observed, or where you get stuck, before I give hints or a reference solution).*
