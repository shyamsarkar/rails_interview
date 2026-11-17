# Transactions in Ruby on Rails

---

## 🔹 What is a Transaction?

A **transaction** is a way to execute multiple database operations **as a single unit**.

👉 Either:
- ✅ All operations succeed → **commit**
- ❌ Any operation fails → **rollback (सब वापस)**

Transactions follow the **ACID** properties:

- **Atomicity:** All operations succeed or none do.
- **Consistency:** Data remains valid and meets its integrity rules.
- **Isolation:** Concurrent transactions do not interfere improperly.
- **Durability:** Committed changes are permanent.

For example, a ₹1,000 bank transfer must both deduct the amount from one account and add it to the other. If either operation fails, both changes should roll back.

---

## 🔹 Why Transactions are Needed?

- Maintain **data consistency**
- Avoid partial updates
- Ensure atomic operations

---

## 🔹 Basic Syntax

```ruby
ActiveRecord::Base.transaction do
  # database operations
end
```

In Rails, `ActiveRecord::Base.transaction` groups database operations into one database transaction. When the block completes successfully, Rails commits; when an exception causes it to fail, Rails rolls back.

```ruby
ActiveRecord::Base.transaction do
  account_a.update!(balance: account_a.balance - 1000)
  account_b.update!(balance: account_b.balance + 1000)
end
```

**Important:** This is a database-level transaction, not a Ruby object-level one. A rollback does not automatically restore the in-memory Active Record objects.

**Interview definition:** Active Record transactions make multiple database operations succeed or fail together, preserving data consistency through commit or rollback.
