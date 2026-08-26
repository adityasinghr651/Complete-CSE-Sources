# PHASE 2 PROJECT — Student Data Manager

This project combines everything from Phase 2 (arrays, objects, nested data, localStorage, array destructuring) on top of everything from Phase 1. Still no DOM yet — this runs in the console/browser dev tools console.

## REQUIREMENTS

Build a small system that manages a list of students, where each student has grades, and the data persists across "reloads" using `localStorage`.

1. Store multiple students as an array of objects.
2. Each student object should have: `name`, `grades` (array of numbers).
3. Add functionality to add a new student.
4. Add functionality to add a grade to an existing student.
5. Calculate each student's average grade.
6. Find the top-performing student.
7. Persist the entire student list to `localStorage` so it survives a page reload.
8. Load existing data from `localStorage` on startup (don't overwrite saved data with an empty list every time the script runs).

## FEATURES

- `addStudent(students, name)` — adds a new student object with an empty `grades` array, returns the updated array.
- `addGrade(students, name, grade)` — finds the student by name and pushes a grade into their `grades` array.
- `getAverage(student)` — returns the average of a single student's grades (handle the case of zero grades — don't divide by zero).
- `getTopStudent(students)` — returns the student object with the highest average.
- `saveStudents(students)` — stringifies and saves the array to `localStorage`.
- `loadStudents()` — reads and parses from `localStorage`, returning an empty array if nothing is saved yet.

## EXPECTED BEHAVIOR

```text
Loaded students: []
Added: Aditya
Added: Priya
Added grade 85 to Aditya
Added grade 90 to Aditya
Added grade 70 to Priya
Aditya's average: 87.5
Priya's average: 70
Top student: Aditya (87.5)
Saved to localStorage.

--- after "reload" (re-running loadStudents) ---
Loaded students: [ { name: "Aditya", grades: [85, 90] }, { name: "Priya", grades: [70] } ]
```

## SUGGESTED FILE STRUCTURE

```text
phase2-student-manager/
  index.html        (minimal — just to give localStorage a browser context)
  student-manager.js
```

Since you don't have DOM manipulation yet (that's Phase 4), the `.html` file just needs to load the `.js` file via `<script src="student-manager.js"></script>` so you have `localStorage` available — all actual interaction happens through `console.log` calls in the script itself, not through any visible UI.

## CONCEPTS BEING TESTED

- Arrays of objects (student list)
- Nested data (each student object contains a nested `grades` array)
- Objects (student shape, property access/updates)
- Array destructuring (optional, but try to use it somewhere — e.g., when unpacking a `[min, max]`-style helper if you write one, or destructuring `Object.entries()` if you explore that)
- localStorage (persistence — stringify on save, parse on load)
- Functions, conditionals, loops (from Phase 1 — finding a student by name requires a loop + conditional)

## IMPLEMENTATION MILESTONES

Work in this order:

1. Design the student object shape on paper first: `{ name: "Aditya", grades: [] }`. Write it out before coding.
2. Write `addStudent(students, name)` — start with a hardcoded empty array, add one student, log the result.
3. Write `addGrade(students, name, grade)` — loop through `students`, find the matching name, push the grade. Handle the "student not found" case (don't crash — log a message instead).
4. Write `getAverage(student)` — loop through `student.grades`, sum them, divide by `grades.length`. Handle `grades.length === 0` explicitly (return `0`, not `NaN`).
5. Write `getTopStudent(students)` — loop through all students, compute each average, track the highest.
6. Write `saveStudents(students)` and `loadStudents()` — get the stringify/parse pipeline right, including the "nothing saved yet" case.
7. Wire it all together: on script start, call `loadStudents()`, do some operations, then `saveStudents()`. Refresh your browser page (or rerun) and confirm `loadStudents()` shows your previously saved data.

## MANUAL TEST CASES

| Action | Expected Result |
|---|---|
| `loadStudents()` on first-ever run (nothing saved) | returns `[]`, doesn't crash |
| `addStudent(students, "Aditya")` | students array now has one entry with `grades: []` |
| `addGrade(students, "Aditya", 85)` | Aditya's `grades` becomes `[85]` |
| `addGrade(students, "Unknown", 50)` | doesn't crash; logs a clear "student not found" message |
| `getAverage({ name: "X", grades: [] })` | returns `0`, not `NaN` |
| `getAverage({ name: "X", grades: [80, 90] })` | returns `85` |
| `getTopStudent(students)` with 2+ students | returns the correct highest-average student object |
| `saveStudents(students)` then reload page, then `loadStudents()` | returns the same data that was saved |

## EDGE CASES TO HANDLE

- A student with zero grades (average calculation, and being considered for "top student" — should a 0-grade student ever "win"? Decide and justify your choice)
- Adding a grade to a name that doesn't exist in the list
- `localStorage` being empty on the very first run (must not crash `JSON.parse`)
- Duplicate student names (not required to solve elegantly — just be aware of it and decide what your `addGrade` does if two students share a name: updates the first match it finds, most likely — that's fine, just be intentional)

## 💻 WHAT YOU SHOULD IMPLEMENT YOURSELF

Everything — no starter code. In particular, think hard about:
- The **stringify/parse boundary**: exactly where in your code does data change from "JS object" to "string" and back? Get this crystal clear before writing it.
- How `addGrade` finds the right student — by looping and comparing `student.name === name`, not by index guessing.
- Whether your functions **mutate the array in place** or **return a new array** — be consistent and be able to explain your choice (this connects directly back to the reference-vs-value discussion from Topics 7–9).

> [!TIP]
> Build it, test against the table above, and share your code + console output when done or stuck — I'll review, not rewrite, per your rules.
