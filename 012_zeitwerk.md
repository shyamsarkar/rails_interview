# Zeitwerk in Rails

## What is Zeitwerk?
**Zeitwerk** is the modern **autoloading system** in Ruby on Rails.  
It automatically loads classes/modules based on file names and folder structure.

👉 No need to use `require` manually.

---

## Why Zeitwerk?
Old Rails autoloader had issues:
- Unpredictable loading
- Naming inconsistencies
- Not thread-safe

👉 Zeitwerk fixes all of these.

---

## Core Idea
**File name must match class/module name**
```

app/models/user.rb              -> class User
app/services/payment_service.rb -> class PaymentService
```

---

## Example
```ruby
# app/models/user.rb
class User
end
```
```ruby
User.new  # Auto-loaded by Zeitwerk
```

---

## Autoloading vs Eager Loading

### Autoloading (Development)
- Loads files when needed
- Faster startup
- Supports reloading

### Eager Loading (Production)
- Loads all files at boot
- Better performance
- No runtime loading issues

---

## Reloading (Dev Feature)
- Detects file changes
- Reloads classes automatically

👉 No need to restart server frequently

---

## Rules (Very Important)

✅ Correct
```
user.rb        -> User
order_item.rb  -> OrderItem
```

❌ Wrong
```
user.rb        -> class Users
payment.rb     -> class PaymentService
```

---

## Common Error
```
uninitialized constant User
```

👉 Causes:
- File name mismatch
- Wrong class name
- Incorrect folder structure

---

## One Line Summary
👉 **Zeitwerk** = Convention-based automatic code loader in Rails