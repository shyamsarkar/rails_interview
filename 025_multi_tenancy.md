# 🏢 Multi-Tenancy in Rails

## 👉 What is Multi-Tenancy?

Multi-tenancy means a **single application** serves **multiple customers (tenants)**, while keeping their data **isolated (अलग)**.

👉 Example:
- SaaS apps like CRM, ERP
- Each company sees only its own data

---

## 🧠 Real-world Example

- Company A and Company B both use your app
- Their data must NOT mix
- But the app codebase is the same

---

## 🧩 Types of Multi-Tenancy in Rails

---

## 🟢 1. Row-Level (Single Database)

### 👉 Idea:
All tenants share same DB and tables
Data is separated using a `tenant_id`

---

### ✅ Example
```ruby
# users table
id | name | tenant_id
```

---

### 🔍 Query Example
```ruby
User.where(tenant_id: current_tenant.id)
```

---

### 👍 Pros & ❌ Cons

| | |
|---|---|
| ✅ Simple to implement | ❌ Risk of data leakage if not careful |
| ✅ Low cost 💰 | ❌ Harder to enforce strict isolation |
| ✅ Easy scaling | |

---

### 🧠 Common Approach

Use default scope:
```ruby
default_scope { where(tenant_id: Current.tenant.id) }
```

---

## 🔴 2. Schema-Level (PostgreSQL)

### 👉 Idea:
Each tenant has a separate schema inside same database

---

### 🧠 Example
```
public.users
tenant1.users
tenant2.users
```

---

### ⚙️ Tools

- Apartment gem (popular)

---

### 👍 Pros & ❌ Cons

| | |
|---|---|
| ✅ Better isolation | ❌ Slightly complex |
| ✅ Same DB, but separated data | ❌ Migration handling tricky |

---

## 🔵 3. Database-Level

### 👉 Idea:
Each tenant has a completely separate database

---

### 🧠 Example
```
db_tenant1
db_tenant2
```

---

### 👍 Pros & ❌ Cons

| | |
|---|---|
| ✅ Strong isolation 🔒 | ❌ Expensive 💰 |
| ✅ Better security | ❌ Hard to manage at scale |

---

## ⚔️ Comparison

| Type | Isolation | Cost | Complexity |
|---|---|---|---|
| Row-level | Low | Low | Easy |
| Schema-level | Medium | Medium | Moderate |
| Database-level | High | High | Complex |

---

## 🧠 How Rails Supports It

Rails doesn't have built-in multi-tenancy, but you can implement using:
- `Current` (thread-safe tenant storage)
- `default_scope`
- Custom middleware
- Gems like Apartment

---

## 🔥 Important Concepts

### 1. Current Tenant
```ruby
Current.tenant = Tenant.find_by(subdomain: request.subdomain)
```

### 2. Data Isolation

Always filter by tenant:
```ruby
Order.where(tenant_id: Current.tenant.id)
```

### 3. Security

Never trust frontend → Always enforce tenant filtering in backend

---

## 🚀 Interview Tips

- Startup SaaS? → Row-level (simple, fast)
- Medium scale? → Schema-level
- Enterprise (banks)? → Database-level

---

## ⚠️ Common Mistakes

- Forgetting `tenant_id` filter ❌
- Using `unscoped` carelessly ❌
- Not testing multi-tenant scenarios ❌