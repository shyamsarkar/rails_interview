# Rails Callbacks – Questions & Answers

---
Callbacks are methods that run automatically at specific points in an object’s lifecycle
— like before saving, after updating, or before deleting a record.

Callbacks = automatic methods triggered during create/update/destroy lifecycle

## Q1. What is the callback sequence when creating an object in Rails?

**Answer:**
When a new record is created and saved, callbacks run in this order:
```
before_validation  
after_validation  
before_save  
around_save (before yield)  
before_create  
around_create (before yield)  
INSERT (DB operation)  
around_create (after yield)  
after_create  
around_save (after yield)  
after_save  
after_commit / after_rollback  
```

---

## Q2. What is the callback sequence when updating an object?

**Answer:**
When an existing record is updated, "create" callbacks are replaced with "update":
```
before_validation  
after_validation  
before_save  
around_save (before yield)  
before_update  
around_update (before yield)  
UPDATE (DB operation)  
around_update (after yield)  
after_update  
around_save (after yield)  
after_save  
after_commit / after_rollback  
```

---

## Q3. What is the callback sequence when destroying an object?

**Answer:**
When a record is deleted:
```
before_destroy  
around_destroy (before yield)  
DELETE (DB operation)  
around_destroy (after yield)  
after_destroy  
after_commit / after_rollback  
```

---

## Q4. What is the difference between create and update callbacks?

**Answer:**
- Create uses: `before_create`, `after_create`, `around_create`
- Update uses: `before_update`, `after_update`, `around_update`
- Rest (`validation`, `save`) are common in both

---

## Q5. What is the role of `before_save` and `after_save`?

**Answer:**
- `before_save` → runs before insert/update
- `after_save` → runs after insert/update
- Applies to both create and update

---

## Q6. What is `around_*` callback?

**Answer:**
- Wraps the operation
- Runs code before and after DB operation using `yield`

**Example:**
```ruby
around_save do |record, block|
  puts "Before save"
  block.call
  puts "After save"
end
```

---

## Q7. What is `after_commit`?

**Answer:**
- Runs after transaction is successfully committed
- Safe for external actions (emails, APIs)

---

## Q8. What is `after_rollback`?

**Answer:**
- Runs when transaction fails and is rolled back
- Useful for cleanup or logging failures

---

## Q9. When should you use callbacks?

**Answer:**
Use callbacks for:
- Data normalization
- Logging
- Small side effects

Avoid:
- Heavy business logic (use services instead)

---

## One Line Summary
👉 Rails callbacks run in a defined lifecycle order for create, update, and destroy operations