### Idempotency (इडेम्पोटेंसी)

> Same request multiple times → **same result**, no duplication

**Definition:** Idempotency means that no matter how many times you perform the same operation, the result will always be the same as if it was done once. In payments, sending the same request 10 times should charge the user **only once**.

---

## ✅ Solution: Idempotency Key

---

## 🧩 Implementation Steps

### 1. Frontend sends a unique idempotency key
```json
{
  "amount": 100,
  "user_id": 123,
  "idempotency_key": "abc-123-xyz"
}
```

### 2. Backend Logic

**Case A: First Request**
- Save in DB:
  - `idempotency_key`
  - `status` (pending/success/failed)
- Process payment

**Case B: Duplicate Request**
- Same key already exists
- Return previous response
- ❌ Do NOT process again

---

## 🗄️ Database Design
```
payments
--------
id
user_id
amount
status
idempotency_key UNIQUE
```

---

## 🔐 Additional Safety

### 1. Database Lock / Transaction
Prevent race condition (रेस कंडीशन)
```sql
SELECT * FROM payments WHERE idempotency_key = ? FOR UPDATE;
```

### 2. Payment Gateway Support
Most gateways support idempotency:
- Stripe
- Razorpay

### 3. Status Handling

| Status | Behavior |
|--------|----------|
| success | Return success |
| pending | Return "processing" |
| failed | Allow retry (new key) |

---

## ⚠️ Common Mistake

❌ "Disable button on frontend"  
👉 Wrong because frontend is not reliable

---

## 💡 Real World Flow

1. User clicks pay
2. Frontend sends request with idempotency key
3. Backend checks DB
4. Calls payment gateway
5. Saves result
6. Retry comes → return stored response

---

## `FOR UPDATE` — Pessimistic Lock

A special `SELECT` that **reads + locks** the row until your transaction is done.


### Without `FOR UPDATE` ❌
```sql
Request 1 → SELECT → key not found → charges ₹239
Request 2 → SELECT → key not found → charges ₹239 again
                                        ↓
                                Total = ₹478 😭
```

### With `FOR UPDATE` ✅
```sql
Request 1 → SELECT ... FOR UPDATE → 🔒 row locked → charges ₹239 → COMMIT
Request 2 → waiting... → row unblocked → key exists → skipped ✅
                                        ↓
                                Total = ₹239 😊
```

---

### Rule: Only works inside a Transaction
```sql
BEGIN;
  SELECT * FROM payments WHERE idempotency_key = 'abc-123' FOR UPDATE;
  INSERT INTO payments (...) VALUES (...);
COMMIT; -- lock released here
```

### In Rails
```ruby
ActiveRecord::Base.transaction do
  payment = Payment.lock.find_by(idempotency_key: params[:key])
end
```

---

### Lock Types

| Type | Query | Behavior |
|------|-------|----------|
| No lock | `SELECT *` | Anyone can read/write |
| Pessimistic | `SELECT * FOR UPDATE` | Others wait until commit |
| Optimistic | `lock_version` column | No lock, checks version at save |

---

> `FOR UPDATE` = "I am reading this row **and** reserving it —
> nobody else can touch it until I'm done." 🔒
