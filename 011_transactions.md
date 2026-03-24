# Transactions in Ruby on Rails

---

## 🔹 What is a Transaction?

A **transaction** is a way to execute multiple database operations **as a single unit**.

👉 Either:
- ✅ All operations succeed → **commit**
- ❌ Any operation fails → **rollback (सब वापस)**

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
