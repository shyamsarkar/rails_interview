# 🔒 Optimistic vs Pessimistic Locking in Rails

Both are used to prevent **race conditions (रेस कंडीशन)** when multiple users try to update the same record.

---

## 🟢 1. Optimistic Locking

### 👉 Idea:
Assume conflicts are **rare**, so don't lock the record upfront.

Rails checks **before saving** whether someone else has modified the record.

---

### ⚙️ How Rails Implements It

Rails uses a special column:
```ruby
lock_version
```

Each update increments this value.

---

### 🧠 How It Works

1. User A loads record → `lock_version = 1`
2. User B loads same record → `lock_version = 1`
3. User A updates → `lock_version = 2`
4. User B tries to update → ❌ Error

---

### ❗ Error Raised
```
ActiveRecord::StaleObjectError
```

---

### ✅ Example
```ruby
product = Product.find(1)
product.name = "New Name"
product.save
```

If record was updated by someone else → exception raised

---

### 👍 When to Use

- Low conflict scenarios
- Read-heavy systems

**Examples:** Blog posts, User profile updates

---

### ⚠️ Pros & ❌ Cons

| | |
|---|---|
| ✅ No DB locking → fast ⚡ | ❌ Need to handle exceptions |
| ✅ Scales well | ❌ Retry logic required |

---

## 🔴 2. Pessimistic Locking

### 👉 Idea:
Assume conflicts are **likely**, so lock the record before updating.

---

### ⚙️ How Rails Implements It

Uses SQL:
```sql
SELECT ... FOR UPDATE
```

Rails method: `.lock`

---

### 🧠 How It Works

1. User A locks record
2. User B tries to access → ⏳ waits
3. User A finishes → User B proceeds

---

### ✅ Example
```ruby
product = Product.lock.find(1)
product.update!(name: "Updated")
```

OR inside a transaction:
```ruby
Product.transaction do
  product = Product.lock.find(1)
  product.update!(stock: product.stock - 1)
end
```

---

### 👍 When to Use

- High conflict scenarios
- Critical operations

**Examples:** Payments 💳, Inventory (stock deduction), Booking systems

---

### ⚠️ Pros & ❌ Cons

| | |
|---|---|
| ✅ Prevents conflicts completely | ❌ Slower (blocking ⏳) |
| ✅ Safe for critical data | ❌ Can cause deadlocks (डेडलॉक) |

---

## ⚔️ Quick Comparison

| Feature | Optimistic Locking | Pessimistic Locking |
|---|---|---|
| Locking | No upfront lock | Locks row |
| Performance | Fast ⚡ | Slower |
| Conflict Handling | Raises error | Prevents conflict |
| Use Case | Low contention | High contention |
| Example | Profile update | Payment system |