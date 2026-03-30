# 🔗 includes vs joins in Rails

Both `includes` and `joins` are used to work with associations in Rails, but they serve **different purposes**.

---

## 🟢 1. includes (Eager Loading)

### 👉 Idea:
Used to **avoid N+1 queries** by loading associated data in advance.

---

### 🧠 Problem (N+1 Query)
```ruby
users = User.all

users.each do |user|
  puts user.posts.count
end
```

👉 This runs:
- 1 query for users
- N queries for posts ❌ Total = N+1 queries (slow)

---

### ✅ Solution with includes
```ruby
users = User.includes(:posts)

users.each do |user|
  puts user.posts.count
end
```

👉 This runs:
- 1 query for users
- 1 query for posts ✅ Total = 2 queries

---

### ⚙️ How It Works

Rails may use:
- Separate queries (most common)
- OR `LEFT OUTER JOIN` (if needed)

---

### 👍 When to Use

- When you need associated data
- To improve performance
- Avoid N+1 problems

---

### ⚠️ Key Point

`includes` is about **loading data**, not filtering.

---

## 🔴 2. joins

### 👉 Idea:
Used to **filter/query** data using associations.

---

### ✅ Example
```ruby
users = User.joins(:posts)
```

👉 Generates SQL:
```sql
SELECT users.* FROM users
INNER JOIN posts ON posts.user_id = users.id
```

---

### 🧠 Behavior

- Uses `INNER JOIN`
- Returns only matching records

👉 Users without posts ❌ NOT included

---

### 👍 When to Use

- When filtering based on association
- When writing conditions on joined tables

---

### ✅ Example with condition
```ruby
User.joins(:posts).where(posts: { published: true })
```

👉 Fetch users who have published posts

---

### ⚠️ Key Point

`joins` does **NOT** preload data → Accessing `user.posts` later can still cause N+1

---

## ⚔️ includes vs joins

| Feature | includes | joins |
|---|---|---|
| Purpose | Avoid N+1 | Filtering |
| Queries | 2 queries (usually) | 1 query |
| Join Type | LEFT OUTER JOIN (sometimes) | INNER JOIN |
| Loads association data | ✅ Yes | ❌ No |
| Returns unmatched records | ✅ Yes | ❌ No |

---

## 🔥 Combining includes + joins

Sometimes you need both:
```ruby
User.includes(:posts)
    .where(posts: { published: true })
    .references(:posts)
```

👉 This forces Rails to use JOIN + preload data