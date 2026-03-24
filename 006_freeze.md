# Freeze in Ruby

## 🔹 What is `freeze`?

`freeze` makes an object **immutable (अपरिवर्तनीय)** — it cannot be modified after freezing.

---

## 🔹 Example

```ruby
str = "hello"
str.freeze

str << " world"   # ❌ FrozenError
```

---

## 🔹 Works with Any Object

```ruby
arr = [1, 2, 3]
arr.freeze

arr << 4   # ❌ Error
```

---

## 🔹 Shallow Freeze (Important)

```ruby
arr = [[1, 2], [3, 4]]
arr.freeze

arr[0] << 99   # ✅ Works
```

> 👉 Only the outer object is frozen — inner objects are **not** frozen.

---

## 🔹 Check if Frozen

```ruby
str.frozen?   # => true
```

---

## 🔹 Use Cases

- Prevent accidental changes to data
- Define constants safely

```ruby
CONFIG = { app: "MyApp" }.freeze
```

---

## 🔹 Frozen String Literal

```ruby
# frozen_string_literal: true
```

> 👉 Freezes **all string literals** in the file — better performance 🚀

---

## 🔹 Modifying a Frozen Object

Use `.dup` to get an unfrozen copy:

```ruby
str = "hello".freeze
new_str = str.dup

new_str << " world"   # ✅ Works
```