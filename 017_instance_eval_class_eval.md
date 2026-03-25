## 🔹 1. `instance_eval`

### ✅ Definition

`instance_eval` executes a block in the context of a specific object (instance).

👉 It changes:
* `self` → becomes the object
* You can access private methods and instance variables

### ✅ Example
```ruby
class User
  def initialize(name)
    @name = name
  end

  private

  def secret
    "This is secret"
  end
end

user = User.new("Shyam")

user.instance_eval do
  puts @name        # accessing instance variable
  puts secret       # accessing private method
end
```

### 🧾 Output
```
Shyam
This is secret
```

### 🎯 Key Points
* Works on object level
* Can access private methods
* Can modify instance variables directly
* Useful in metaprogramming

---

## 🔹 2. `class_eval` (aka `module_eval`)

### ✅ Definition

`class_eval` executes a block in the context of a class, allowing you to define or modify instance methods dynamically.

👉 It changes:
* `self` → becomes the class
* You define instance methods of that class

### ✅ Example
```ruby
class User
end

User.class_eval do
  def greet
    "Hello!"
  end
end

user = User.new
puts user.greet
```

### 🧾 Output
```
Hello!
```

### 🎯 Key Points
* Works on class level
* Used to add methods dynamically
* Common in Rails DSLs (like associations, validations)

---

## ⚖️ Difference Between `instance_eval` and `class_eval`

| Feature | `instance_eval` | `class_eval` |
|---|---|---|
| Context (`self`) | Specific object | Class |
| Use case | Modify object behavior | Define instance methods |
| Scope | Single object | Entire class |
| Access | Instance vars + private methods | Defines methods for instances |