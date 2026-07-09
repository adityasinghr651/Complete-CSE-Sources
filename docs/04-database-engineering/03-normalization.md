# Day 15: Normalization (1NF, 2NF, 3NF)  
*(Detailed, step-by-step, from first principles — with definitions, simple language, intuition, diagrams, and production examples)*

---

## SECTION 1: INTUITION (Why Normalization Matters)

Think of a **messy spreadsheet**:

```text
orders (bad design)
order_id | customer_name | customer_email | product_name | product_price | quantity
1        | Raj           | raj@example.com | Laptop       | 50000         | 1
2        | Raj           | raj@example.com | Mouse        | 500           | 2
3        | Priya         | priya@example.com | Laptop     | 50000         | 1
```

Problems:
- `customer_name` and `customer_email` repeated for Raj.
- `product_price` repeated for Laptop.
- If Raj changes email, you must update 2 rows.
- If Laptop price changes, you must update 2 rows.

**Normalization** = reorganizing this spreadsheet into clean tables:

```text
customers (id, name, email)
products (id, name, price)
orders (id, customer_id, product_id, quantity)
```

Now:
- Each customer stored once.
- Each product stored once.
- Updates are easy.
- No duplicates.

> **Simple Analogy:**  
> - **Normalization** = Organizing data properly.  
> - Remove duplicates.  
> - Store each fact exactly once.  
> - Make updates, inserts, and deletes easy.

---

## SECTION 2: CORE DEFINITIONS

### 2.1 What is Normalization? (Definition)

**Normalization** = the process of organizing data in a database to:
1. **Reduce redundancy** (duplicate data).
2. **Improve data integrity** (consistency).
3. **Make schema easier to maintain**.

Goal:  
Store each fact **once**, in the **right place**.

Key idea:  
Avoid:
- Repeating data.
- Inconsistent updates.
- Data anomalies (insert, update, delete anomalies).

---

### 2.2 Why Do We Need Normalization?

Without normalization:
- You store same data many times.
- If you update one copy, others remain old → **inconsistency**.
- Inserting or deleting data can cause errors (**anomalies**).

Example anomalies:
- **Insert anomaly**: Can't add a product without an order.
- **Update anomaly**: Change price in one row, but not others.
- **Delete anomaly**: Delete an order, lose product info.

---

## SECTION 3: NORMAL FORMS (1NF, 2NF, 3NF)

Normalization is done in steps: **normal forms**.
Each normal form has rules. You must satisfy previous normal form first.

---

## SECTION 4: FIRST NORMAL FORM (1NF)

### 4.1 Definition

**First Normal Form (1NF)** = a table is in 1NF if:
1. **Each column contains atomic (single) values**.
2. **No repeating groups** (e.g., multiple values in one column).
3. **Each row is unique** (has a primary key).

Key rule:  
**One value per cell.**

---

### 4.2 Example: Unnormalized Table

```text
students (bad)
id | name     | courses
1  | Raj      | Math, Science
2  | Priya    | English
```

Problems:
- `courses` has multiple values: "Math, Science".
- Not atomic.

---

### 4.3 Example: 1NF Table

```text
students (1NF)
id | name  | course
1  | Raj   | Math
1  | Raj   | Science
2  | Priya | English
```

Now:
- Each row has **one course**.
- Each column has **one value**.
- Primary key: `(id, course)`.

---

### 4.4 Rules for 1NF

1. No arrays/lists in a column.
2. Each column has one value.
3. Primary key exists.

---

## SECTION 5: SECOND NORMAL FORM (2NF)

### 5.1 Definition

**Second Normal Form (2NF)** = a table is in 2NF if:
1. It is in **1NF**.
2. **No partial dependency**:
   - Every non-key column depends on **the whole primary key**, not just part of it.
   - This applies when primary key has **multiple columns**.

Key rule:  
**If primary key has multiple columns, all other columns must depend on ALL of them.**

---

### 5.2 Example: Table Not in 2NF

```text
order_items (BAD)
order_id | product_id | product_name | quantity | customer_name
1        | 10         | Laptop       | 1        | Raj
1        | 11         | Mouse        | 2        | Raj
2        | 10         | Laptop       | 1        | Priya
```

Primary key: `(order_id, product_id)`.

Problems:
- `product_name` depends only on `product_id` (not full key).
- `customer_name` depends only on `order_id` (not full key).

This is **partial dependency**.

---

### 5.3 Example: 2NF Table

Split into:

```text
orders (2NF)
order_id | customer_name
1        | Raj
2        | Priya

products (2NF)
product_id | product_name
10         | Laptop
11         | Mouse

order_items (2NF)
order_id | product_id | quantity
1        | 10         | 1
1        | 11         | 2
2        | 10         | 1
```

Now:
- `product_name` depends fully on `product_id`.
- `customer_name` depends fully on `order_id`.
- No partial dependency.

---

### 5.4 Rules for 2NF

1. Table must be in 1NF.
2. No partial dependency on part of primary key.
3. If primary key has single column, table is automatically in 2NF.

---

## SECTION 6: THIRD NORMAL FORM (3NF)

### 6.1 Definition

**Third Normal Form (3NF)** = a table is in 3NF if:
1. It is in **2NF**.
2. **No transitive dependency**:
   - Non-key columns should not depend on other non-key columns.
   - Only depend on primary key.

Key rule:  
**Every non-key column must depend only on primary key.**

---

### 6.2 Example: Table Not in 3NF

```text
employees (BAD)
employee_id | name | department_id | department_name | department_location
1           | Raj  | 10            | Engineering     | Building A
2           | Priya| 10            | Engineering     | Building A
3           | Raj  | 20            | Marketing       | Building B
```

- `department_name` depends on `department_id`.
- `department_location` depends on `department_id`.
- But `department_id` is **not** primary key → **transitive dependency**.

---

### 6.3 Example: 3NF Table

Split into:

```text
employees (3NF)
employee_id | name | department_id
1           | Raj  | 10
2           | Priya| 10
3           | Raj  | 20

departments (3NF)
department_id | department_name | department_location
10            | Engineering     | Building A
20            | Marketing       | Building B
```

Now:
- `department_name` and `department_location` depend directly on `department_id` (primary key of departments).
- No transitive dependency.

---

### 6.4 Rules for 3NF

1. Table must be in 2NF.
2. No transitive dependency.
3. All non-key columns depend only on primary key.

---

## SECTION 7: VISUAL DIAGRAMS

### Diagram 1: Normalization Steps

```text
Unnormalized Table
  |
  | Apply 1NF rules
  v
1NF Table (atomic values, no repeating groups)
  |
  | Apply 2NF rules
  v
2NF Table (no partial dependency)
  |
  | Apply 3NF rules
  v
3NF Table (no transitive dependency)
```

---

### Diagram 2: Transitive Dependency Example

```text
employees (BAD - not 3NF)
employee_id → department_id → department_name
              (not PK)       (depends on dept_id, not emp_id)

departments (GOOD - 3NF)
department_id → department_name
department_id → department_location
```

---

## SECTION 8: PRACTICAL NORMALIZATION FOR APPS

For web apps:
- Aim for **3NF**.
- Don't over-normalize to BCNF unless needed.
- Sometimes you intentionally **denormalize** for performance:
  - Store extra data to avoid joins.
  - Example: store `user_name` in `posts` table to avoid joining `users` every time.

Tradeoff:
- Normalization: good for data integrity.
- Denormalization: good for performance.

---

## SECTION 9: COMMON MISTAKES

1. **No normalization**  
   - Duplicate data, inconsistencies. Aim for 3NF.
2. **Over-normalization**  
   - Too many joins, slow queries. Sometimes denormalize for performance.
3. **Ignoring 1NF rules**  
   - Multiple values in one column. One value per cell.
4. **Not checking partial dependency**  
   - 2NF violated. Split tables.
5. **Not checking transitive dependency**  
   - 3NF violated. Split tables.

---

## SECTION 10: INTERVIEW-STYLE QUESTIONS

1. What is normalization? Why do we need it?  
2. What are insertion, update, and delete anomalies?  
3. Explain 1NF with an example.  
4. Explain 2NF with an example.  
5. Explain 3NF with an example.  
6. What is a partial dependency?  
7. What is a transitive dependency?  
8. How do you convert a table to 1NF?  
9. How do you convert a table to 2NF?  
10. How do you convert a table to 3NF?

---

## SECTION 11: REVISION NOTES (CHEAT SHEET)

- **Normalization**: Reduce redundancy, improve integrity.
- **1NF**: Atomic values. No repeating groups. Primary key exists.
- **2NF**: In 1NF. No partial dependency (on full key).
- **3NF**: In 2NF. No transitive dependency. All non-key columns depend only on primary key.
- **Goal**: Store each fact once.

---

## SECTION 12: HANDS-ON ASSIGNMENT

Normalize this **unnormalized table** to 3NF:

```text
orders (bad)
order_id | customer_name | customer_email | product_name | product_price | quantity
1        | Raj           | raj@example.com | Laptop       | 50000         | 1
2        | Raj           | raj@example.com | Mouse        | 500           | 2
3        | Priya         | priya@example.com | Laptop     | 50000         | 1
```

### Tasks
1. Identify problems (redundancy, anomalies).
2. Convert to 1NF.
3. Convert to 2NF (split tables).
4. Convert to 3NF (split tables).
5. Write final schema with tables, columns, foreign keys.

---

## SECTION 13: MINI PROJECT

Normalize a **real schema**:
- Take your blog app (users, posts, comments).
- Check if it's in 3NF.
- If not, normalize it.
- Document changes.
