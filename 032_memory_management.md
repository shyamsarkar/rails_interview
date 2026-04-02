# 🔹 Memory Management in Ruby

Core Idea: Ruby automatically allocates and frees memory — you don’t manually manage it like in C/C++.

Objects are allocated on the heap and cleaned up when no longer referenced. Modern Ruby also supports compaction to reduce fragmentation.

🧠 One-Line Summary
Ruby uses automatic garbage collection with generational + incremental optimizations to efficiently manage memory.

# 🔹 Stack vs Heap in Ruby (Examples)

This guide shows clearly:

👉 what goes to **Stack (स्टैक)**
👉 what goes to **Heap (हीप)**

---

# 🔹 Example 1: Simple Variable

```ruby
name = "Shyam"
```

### 📌 Memory Breakdown

* **Stack:**

  * `name` → stores reference

* **Heap:**

  * `"Shyam"` → actual string object

---

# 🔹 Example 2: Multiple References

```ruby
a = "hello"
b = a
```

### 📌 Memory Breakdown

* **Stack:**

  * `a` → reference to object
  * `b` → reference to SAME object

* **Heap:**

  * `"hello"` → single object

👉 Important: No duplicate object created

---

# 🔹 Example 3: Array

```ruby
arr = [1, 2, 3]
```

### 📌 Memory Breakdown

* **Stack:**

  * `arr` → reference

* **Heap:**

  * `[1, 2, 3]` → array object
  * `1, 2, 3` → also objects (in Ruby everything is object)

---

# 🔹 Example 4: Method Call

```ruby
def greet
  message = "Hi"
  puts message
end

greet
```

### 📌 Memory Breakdown

* **Stack:**

  * `greet` method frame
  * `message` → reference

* **Heap:**

  * `"Hi"` → string object

👉 After method finishes:

* Stack frame removed
* `"Hi"` becomes eligible for GC (if no reference)

---

# 🔹 Example 5: Object Creation

```ruby
class User
end

user = User.new
```

### 📌 Memory Breakdown

* **Stack:**

  * `user` → reference

* **Heap:**

  * `User` instance object

---

# 🔹 Example 6: Nested Method Calls (Stack Behavior)

```ruby
def a
  b
end

def b
  c
end

def c
  puts "Hello"
end

a
```

### 📌 Stack Flow (LIFO)

```
Stack:
------
c()
b()
a()
```

👉 Execution:

* `a` → calls `b`
* `b` → calls `c`
* `c` finishes → popped
* then `b` → popped
* then `a` → popped

---

# 🔹 Example 7: Hash Object

```ruby
user = { name: "Shyam", age: 25 }
```

### 📌 Memory Breakdown

* **Stack:**

  * `user` → reference

* **Heap:**

  * Hash object
  * `"Shyam"` string
  * `25` integer

---

# 🔥 Important Ruby Insight

👉 Even integers are objects in Ruby:

```ruby
x = 10
```

* `x` → stack
* `10` → heap (internally optimized, but conceptually heap)

---

# 🧠 Visual Summary

```
Stack                          Heap
------                         ------------------------
name  -----------→             "Shyam"
arr   -----------→             [1,2,3]
user  -----------→             #<User:0x...>
message ------→                "Hi"
```

---

# 🚨 Interview-Level Insight

👉 Trick question:
**“Where are variables stored in Ruby?”**

✅ Best answer:

> “Local variables are stored on the stack as references, while actual objects are stored on the heap.”

---

# 🧠 Easy Rule

* Variable → **Stack**
* Object → **Heap**

---

# ⚡ Advanced Insight

Ruby internally uses:

* **Stack for execution frames**
* **Heap for all objects**
* **Garbage Collector cleans heap only (not stack)**

---

# 🧠 One-Line Summary

> Stack stores references and execution context, while heap stores actual objects in Ruby.
