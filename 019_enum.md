### Q. Which enum syntax is newer in Rails?

**Answer:**

In Ruby on Rails, the newer syntax (introduced in Rails 7) is:

```ruby
enum :status, { active: 0, inactive: 1, blocked: 2 }
```

The older syntax is:

```ruby
enum status: { active: 0, inactive: 1, blocked: 2 }
```

👉 The new syntax is preferred because it is more consistent with Rails conventions like `belongs_to :user`.

**One-liner:**  
Rails 7+ → `enum :status, {...}` (recommended)