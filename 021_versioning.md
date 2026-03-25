# Scope vs Namespace in Rails Routes

---

## Q. What is the difference between `scope` and `namespace` in Rails routes?

**Answer:**

In Ruby on Rails, both `scope` and `namespace` are used to organize routes, but they behave differently.

---

## 🔹 namespace

- Adds URL prefix automatically  
- Adds controller module automatically  

### Example:

```ruby
namespace :admin do
  resources :users
end
```

👉 URL → `/admin/users`  
👉 Controller → `Admin::UsersController`

---

## 🔹 scope

- More flexible  
- Can customize path and/or module  

### Example 1: Only path

```ruby
scope "/admin" do
  resources :users
end
```

👉 URL → `/admin/users`  
👉 Controller → `UsersController`

---

### Example 2: Only module

```ruby
scope module: "admin" do
  resources :users
end
```

👉 URL → `/users`  
👉 Controller → `Admin::UsersController`

---

### Example 3: Path + module

```ruby
scope path: "/admin", module: "admin" do
  resources :users
end
```

👉 URL → `/admin/users`  
👉 Controller → `Admin::UsersController`

---

## ⚖️ Key Differences

| Feature     | namespace            | scope              |
|------------|---------------------|--------------------|
| URL prefix | Yes                 | Optional           |
| Module     | Yes                 | Optional           |
| Flexibility| Less                | More               |

---

## 🧠 One-liner

- namespace = path + module together  
- scope = flexible (custom behavior)