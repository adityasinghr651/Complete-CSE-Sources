# Topic 10: Local Storage

## WHY
Normally, when a user refreshes a page or closes the browser tab, all your JavaScript variables disappear — memory is wiped. But sometimes you want small bits of data to **survive** across page reloads: a theme preference, a saved draft, a "remember me" flag. `localStorage` gives you a simple way to persist small amounts of data **in the browser itself**, without needing a server or database.

## WHAT
`localStorage` is a browser API that lets you store key-value pairs **as strings**, persisted even after the browser is closed and reopened (until explicitly cleared). It's scoped per-origin (per website domain).

## MENTAL MODEL

Think of `localStorage` as a **small notebook the browser keeps for each website**, separate from your JavaScript variables, that doesn't get erased on refresh:

```text
   JS variables (in memory)          localStorage (on disk, per-origin)
   ┌─────────────────┐               ┌─────────────────────┐
   │ let x = 5;        │              │ "theme" → "dark"      │
   │ (wiped on reload) │              │ "username" → "adi"    │
   └─────────────────┘               │ (survives reload)     │
                                     └─────────────────────┘
```

Critically: **everything stored is a string.** If you store an object or array, you must convert it to a string first (`JSON.stringify`) and convert it back when reading (`JSON.parse`).

## SYNTAX

```javascript
// Storing
localStorage.setItem("username", "aditya");

// Reading
const name = localStorage.getItem("username");
console.log(name); // "aditya"

// Removing one item
localStorage.removeItem("username");

// Clearing everything
localStorage.clear();

// Storing an object/array — must stringify first
const user = { name: "Aditya", age: 21 };
localStorage.setItem("user", JSON.stringify(user));

// Reading it back — must parse
const savedUser = JSON.parse(localStorage.getItem("user"));
console.log(savedUser.name); // "Aditya"
```

## SMALLEST POSSIBLE EXAMPLE

```javascript
localStorage.setItem("greeting", "hello");
console.log(localStorage.getItem("greeting")); // "hello"
```

> [!NOTE]
> This must run in a **browser environment** (open an HTML file, or use browser dev tools console) — `localStorage` doesn't exist in plain Node.js by default, since it's a browser API, not a core JS language feature.

## MANUAL IMPLEMENTATION
Not something to build from scratch — it's a browser-provided API. The relevant manual practice is building the **stringify → store → retrieve → parse** pipeline yourself correctly, since forgetting either step is the single most common bug here.

## PRACTICAL USE
- Saving a user's theme preference (dark/light mode) across visits
- Persisting a shopping cart so it survives a page refresh
- Saving form draft data so users don't lose progress
- Storing a "don't show this again" dismissal flag for a banner/modal

## EDGE CASES

1. **Everything is stored as a string — even numbers and booleans**:
```javascript
localStorage.setItem("count", 5);
const count = localStorage.getItem("count");
console.log(typeof count); // "string", not "number"!
console.log(count + 1);    // "51" — string concatenation, not math!
console.log(Number(count) + 1); // 6 — must convert manually
```

2. **Reading a key that was never set returns `null`, not `undefined`**:
```javascript
console.log(localStorage.getItem("doesNotExist")); // null
```

3. **`JSON.parse(null)` throws**, which matters if you forget to check for missing keys:
```javascript
const data = JSON.parse(localStorage.getItem("missingKey")); // TypeError-ish issue: actually returns null for parse(null)? 
```
Careful here — `JSON.parse(null)` actually returns `null` without throwing (it coerces `null` to the string `"null"` first) — but `JSON.parse(undefined)` **does** throw. The safer habit is always checking existence before parsing:
```javascript
const raw = localStorage.getItem("user");
const user = raw ? JSON.parse(raw) : null;
```

4. **Storage limits** — typically around 5–10MB per origin depending on the browser. Trying to store beyond this throws a `QuotaExceededError`.

5. **Data is per-origin, not per-tab or per-session** — all tabs open to the same website share the same `localStorage`.

## COMMON MISTAKES
- Forgetting `JSON.stringify`/`JSON.parse` when storing/reading objects or arrays
- Assuming numbers/booleans come back as their original type instead of strings
- Not checking if a key exists before parsing, risking a crash on `null`
- Storing sensitive data (passwords, tokens) in `localStorage` — it's plain text, readable by any JS running on the page (including malicious third-party scripts if the site has an XSS vulnerability) — a real security consideration, not just theory

## DEBUGGING EXERCISE

```javascript
localStorage.setItem("cart", [1, 2, 3]);
const cart = localStorage.getItem("cart");
console.log(cart[0]);
```

> [!NOTE]
> *(Reason through it: what does this actually print, and why might it surprise someone expecting `1`?)*
> 
> **Answer:** `localStorage.setItem` doesn't accept arrays/objects directly — when you pass a non-string value, it's automatically coerced to a string via `.toString()`, so the array `[1, 2, 3]` becomes the string `"1,2,3"`. Reading it back gives the string `"1,2,3"`, and `cart[0]` on a string returns the **character** at index 0, which is `"1"` (a one-character string), not the number `1` from the original array. This silently "works" without an error, which makes it a dangerous logical bug — the fix is `JSON.stringify([1,2,3])` on the way in and `JSON.parse(...)` on the way out.

## WHERE THIS APPEARS IN REAL SOFTWARE
- Theme toggles (dark/light mode) that persist across visits
- "Remember me" style conveniences (though real auth tokens need more careful, security-conscious handling than raw `localStorage`)
- Client-side caching of non-sensitive data to reduce repeated API calls
- Saving in-progress form data or draft content

## REAL-WORLD CONSIDERATION
- **Storage limits**: ~5–10MB per origin — not for large datasets
- **Serialization overhead**: every read/write to complex data means stringify/parse, which has a cost at scale
- **Stale data**: cached data in `localStorage` can drift out of sync with the server — real apps need a strategy for invalidating/refreshing it
- **Sensitive data concerns**: never store tokens/passwords in plain `localStorage` in security-conscious applications
- **Client-side-only availability**: `localStorage` doesn't exist during server-side rendering (relevant later once you touch Next.js) — code that assumes it's always available can crash in non-browser contexts

> **WHERE YOU WILL SEE THIS LATER:**
> ```text
> localStorage
>    ↓
> Persisting React state across reloads (e.g., a cart or theme context)
>    ↓
> Client-side caching patterns before reaching for a real backend/database
> ```

## INTERVIEW QUESTION

**Q: Why can't you store an object directly in `localStorage`? What steps are needed to correctly save and retrieve an object?**

*Answer out loud before checking anything — mention that everything is stored as a string, and walk through the `JSON.stringify`/`JSON.parse` pair.*

---

## 💻 WRITE WITHOUT AI

*(You'll need a browser console or a simple HTML file with a `<script>` tag to test these — plain Node.js won't have `localStorage` built in.)*

1. Store your name in `localStorage` under the key `"myName"`, then retrieve and log it.
2. Store a number (e.g., `42`) in `localStorage`, retrieve it, and demonstrate the string-coercion gotcha by trying to add `1` to it directly (log the wrong result), then fix it with `Number(...)` (log the correct result).
3. Store an object `{ theme: "dark", fontSize: 16 }` correctly using `JSON.stringify`, then retrieve and parse it back, logging `theme`.
4. Write a small function `getSavedUser()` that reads a `"user"` key from `localStorage`, returning `null` if it doesn't exist (don't let it crash on a missing key).
5. (Harder) Simulate a saved shopping cart: store an array of item names, add a new item to the array (remember: you have to read, parse, modify, stringify, and save again — `localStorage` doesn't let you push directly into stored data), and log the final array after "reloading" (re-reading from `localStorage`).

*(Reply with your attempts or where you get stuck before I give hints or the reference solution).*
