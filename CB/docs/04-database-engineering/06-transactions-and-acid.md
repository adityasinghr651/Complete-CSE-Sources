# Day 18: Transactions (ACID, Isolation Levels, Deadlocks)  
*(Detailed, step-by-step, from first principles — with definitions, simple language, intuition, diagrams, and production examples)*

---

## SECTION 1: INTUITION (Why Transactions Matter)

Think of a **bank transfer**:

1. User A sends ₹100 to User B.
2. Steps:
   - Subtract ₹100 from A's account.
   - Add ₹100 to B's account.

Problem:
- If step 1 succeeds, but step 2 fails (system crash):
  - A lost ₹100.
  - B didn't get ₹100.
  - Money lost.

**Transaction** = group both steps together:
- Either **both succeed** OR **both fail**.
- No partial state.

> **Simple Analogy:**  
> - **Transaction** = Bring a group of queries together into a single unit.  
> - The entire group must either fully succeed or fully fail.  
> - There cannot be a partial state.

---

## SECTION 2: CORE DEFINITIONS

### 2.1 What is a Transaction? (Definition)

**Transaction** = a group of database operations that:
1. Execute **as a single unit**.
2. Either **all succeed** OR **all fail**.
3. Maintain **data integrity**.

Key idea:  
**All or nothing** (no partial state).

---

### 2.2 Transaction Commands

```sql
BEGIN;

-- Step 1
UPDATE accounts SET balance = balance - 100 WHERE id = 1;

-- Step 2
UPDATE accounts SET balance = balance + 100 WHERE id = 2;

COMMIT;
```

If any step fails:

```sql
ROLLBACK;
```

Revert all changes.

---

## SECTION 3: ACID PROPERTIES

### 3.1 What is ACID? (Definition)

**ACID** = 4 properties that ensure transactions are reliable:
1. **Atomicity**
2. **Consistency**
3. **Isolation**
4. **Durability**

Goal:  
Make transactions **safe, reliable, predictable**.

---

### 3.2 Atomicity

**Atomicity** = transaction completes **fully** or **not at all**.
- No partial execution.
- If any step fails → rollback all.

Example:
Bank transfer:
- Subtract ₹100 from A.
- Add ₹100 to B.

If step 2 fails:
- Rollback step 1.
- A's balance unchanged.

---

### 3.3 Consistency

**Consistency** = database stays **valid** after transaction.
- Rules/constraints must hold.
- No invalid data.

Example:
- Balance must not be negative.
- If transaction would make balance negative → reject.

---

### 3.4 Isolation

**Isolation** = transactions don't interfere with each other.
- Each transaction runs independently.
- One transaction's changes not visible to others until committed.

Example:
- Transaction A updates balance.
- Transaction B reads balance.
- B sees **old value** until A commits.

---

### 3.5 Durability

**Durability** = committed data is **permanent**.
- Even if system crashes, data saved.
- Written to disk.

Example:
- Transaction commits.
- System crashes immediately.
- Data still in DB.

> ✅ **[Principal Engineer Note]: The Write-Ahead Log (WAL)**
> *How does a database guarantee Durability without destroying performance? If a DB had to write massive B-Tree rebalances to the SSD for every single transaction, it would be incredibly slow. Instead, it uses a **Write-Ahead Log (WAL)**. When you commit, the DB only writes a tiny, fast, append-only log to the SSD saying "I am going to change this". It updates the actual B-Tree in RAM and flushes it to disk later. If the power dies, on reboot, the DB reads the WAL and replays the changes!*

---

## SECTION 4: ISOLATION LEVELS

### 4.1 What are Isolation Levels? (Definition)

**Isolation Levels** = how much one transaction can see another transaction's changes.

Levels (from least to most isolated):
1. **Read Uncommitted**
2. **Read Committed**
3. **Repeatable Read**
4. **Serializable**

---

### 4.2 Read Uncommitted

- Transaction can see **uncommitted changes** from others.
- Problem: **dirty reads**.

Example:
- Transaction A updates balance (not committed).
- Transaction B reads balance (sees uncommitted value).
- A rolls back.
- B read wrong value.

---

### 4.3 Read Committed

- Transaction sees **only committed changes**.
- Default in PostgreSQL.

Example:
- Transaction A updates balance (not committed).
- Transaction B reads balance (sees old value).
- A commits.
- B sees new value.

Problem: **non-repeatable reads**.

---

### 4.4 Repeatable Read

- Transaction sees **same data** throughout.
- No changes visible even if others commit.

Example:
- Transaction A reads balance = 100.
- Transaction B updates balance to 200 (commits).
- Transaction A reads balance again = 100 (same).

Problem: **phantom reads** (new rows appear).

---

### 4.5 Serializable

- Transactions run **one at a time**.
- Most isolated.
- Slow, but safest.

No dirty reads, non-repeatable reads, or phantom reads.

> ✅ **[Principal Engineer Note]: MVCC and Optimistic Locking**
> *Saying Serializable runs "one at a time" is conceptually true but mechanically false in modern DBs like Postgres. Instead of literally locking the entire database (Pessimistic Locking), Postgres uses **MVCC (Multi-Version Concurrency Control)**. It allows transactions to run concurrently, but if it detects that Transaction B modified rows that Transaction A relied on, it aborts Transaction A with a serialization error. This is called **Optimistic Concurrency Control**. You must wrap your code in a retry loop when using Serializable!*

---

## SECTION 5: DEADLOCKS

### 5.1 What is a Deadlock? (Definition)

**Deadlock** = two transactions block each forever.

Example:
- Transaction A:
  - Locks row 1.
  - Wants row 2.
- Transaction B:
  - Locks row 2.
  - Wants row 1.

A waits for B.  
B waits for A.  
**Forever** → deadlock.

---

### 5.2 How Database Handles Deadlocks

Database detects deadlock:
- Rolls back one transaction.
- Other transaction continues.

Example:
- DB rolls back B.
- A continues.

---

### 5.3 How to Prevent Deadlocks

1. **Lock in same order**:
   - Always lock row 1 first, then row 2.
2. **Short transactions**:
   - Less time locked.
3. **Use lower isolation levels**:
   - Less locking.

---

## SECTION 6: COMMON MISTAKES

1. **Not using transactions**  
   - Partial state possible. Use transactions for critical operations.
2. **Not handling rollback**  
   - Data inconsistent if error. Always rollback on error.
3. **Long transactions**  
   - Deadlocks, slow. Short transactions.
4. **Ignoring isolation levels**  
   - Wrong behavior. Choose correct level.

---

## SECTION 7: INTERVIEW-STYLE QUESTIONS

1. What is a transaction? Why do we use it?  
2. What is ACID? Explain each property.  
3. What is atomicity?  
4. What is consistency?  
5. What is isolation?  
6. What is durability?  
7. What are isolation levels? List them.  
8. What is a dirty read?  
9. What is a deadlock? How do you prevent it?  
10. Write a transaction for bank transfer.

---

## SECTION 8: REVISION NOTES (CHEAT SHEET)

- **Transaction**: Group of operations. All or nothing.
- **ACID**:
  - **Atomicity**: fully success or fail.
  - **Consistency**: database valid.
  - **Isolation**: transactions independent.
  - **Durability**: committed data permanent.
- **Isolation levels**:
  - Read Uncommitted (dirty reads).
  - Read Committed (default).
  - Repeatable Read.
  - Serializable (most isolated).
- **Deadlock**:
  - Two transactions block each forever.
  - Prevent: lock in same order, short transactions.

---

## SECTION 9: HANDS‑ON ASSIGNMENT

Implement a **bank transfer transaction**:

### Schema

```sql
accounts (
  id INTEGER PRIMARY KEY,
  name VARCHAR(255),
  balance DECIMAL
);
```

### Tasks
1. Create 2 accounts:
   - A: balance = 1000.
   - B: balance = 500.
2. Write transaction:
   - Transfer ₹100 from A to B.
   - Subtract from A.
   - Add to B.
   - COMMIT if both succeed.
   - ROLLBACK if any fails.
3. Test:
   - Normal case (both succeed).
   - Error case (second step fails → rollback).
4. Check balances after transaction.

---

## SECTION 10: MINI PROJECT

Build a **transaction-based order system**:
- Create order.
- Create order items.
- Update inventory.
- All in one transaction.
- Rollback if any fails.
