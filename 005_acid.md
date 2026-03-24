# ACID Properties in Databases

ACID properties are a set of principles used in databases (especially in relational databases like MySQL, PostgreSQL) to ensure **reliable and consistent transactions**.

---

## 🔹 ACID = Atomicity, Consistency, Isolation, Durability

---

## 1. Atomicity (All or Nothing)
- A transaction either **fully completes or fully fails**.
- No partial updates are allowed.

### 👉 Example:
If you transfer ₹100 from A → B:
- Deduct from A ✅  
- Add to B ❌ (fails)

➡️ Then deduction from A will also be **rolled back**.

---

## 2. Consistency (Valid State)
- Database always moves from **one valid state to another valid state**.
- Rules like constraints, triggers, etc. are never violated.

### 👉 Example:
- If balance cannot go below 0, the transaction will fail if it tries to break this rule.

---

## 3. Isolation (No Interference)
- Multiple transactions run **independently** without affecting each other.
- Intermediate states are not visible to other transactions.

### 👉 Example:
- Two users updating the same account:
  - One transaction should not see half-done changes of another.

### 💡 Isolation Levels:
- Read Uncommitted  
- Read Committed  
- Repeatable Read  
- Serializable  

---

## 4. Durability (Permanent Changes)
- Once a transaction is committed, changes are **permanently saved**.
- Even if system crashes, data is not lost.

### 👉 Example:
- After successful payment, data remains safe even after power failure.

---

## 🔥 Simple Way to Remember

- **A** → All or Nothing  
- **C** → Correct State  
- **I** → Independent Execution  
- **D** → Don’t Lose Data  

---

## 💡 Real-world Analogy (Bank Transaction)

Think of ATM withdrawal:

- **Atomicity** → Either cash comes out fully or not at all  
- **Consistency** → Balance updates correctly  
- **Isolation** → Your transaction doesn’t mix with others  
- **Durability** → Once done, it stays recorded  

---

## 🚀 Summary

ACID ensures that database transactions are:
- Reliable  
- Consistent  
- Safe even in failures  

It is a fundamental concept for **system design and backend development interviews**.