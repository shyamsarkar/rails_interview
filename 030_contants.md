## Constants in Ruby

### 1. Basic Constant Definition
```ruby
MAX_SIZE = 100
APP_NAME = "MyApp"

puts APP_NAME  # => "MyApp"
```

Constants start with an **uppercase letter**. By convention, use `ALL_CAPS` for true constants.

---

### 2. Constants Inside Classes & Modules
```ruby
class Circle
  PI = 3.14159

  def area(radius)
    PI * radius ** 2
  end
end

# Accessing from outside
Circle::PI   # => 3.14159
```

---

### 3. Constants Inside Modules (Namespacing)
```ruby
module Currency
  USD = "USD"
  INR = "INR"
  EUR = "EUR"
end

Currency::INR   # => "INR"
Currency::USD   # => "USD"
```

---

### 4. Freezing Constants

Ruby **warns** but still allows mutation of constants. Use `freeze` to truly prevent it:
```ruby
ROLES = %w[admin member guest].freeze
CONFIG = { retries: 3, timeout: 30 }.freeze

ROLES << "superadmin"   # => raises FrozenError
```

---

### 5. Constants are Accessible in Subclasses
```ruby
class Animal
  CATEGORY = "Living Being"
end

class Dog < Animal
  def info
    CATEGORY   # accessible directly
  end
end

Dog.new.info     # => "Living Being"
Dog::CATEGORY    # => "Living Being"
```

---

### 6. Constant Lookup (`::`)
```ruby
module Outer
  LABEL = "outer"

  module Inner
    LABEL = "inner"

    def self.outer_label
      Outer::LABEL   # explicitly reference outer
    end
  end
end

Outer::Inner::LABEL          # => "inner"
Outer::Inner.outer_label     # => "outer"
```

---

### 7. Reassigning a Constant (Warning)
```ruby
MY_CONST = 10
MY_CONST = 20   # => warning: already initialized constant MY_CONST
```

Ruby won't stop you, but it will warn — which is a signal that you're doing something unintended.

---

### Quick Reference

| Concept | Syntax |
|---|---|
| Define a constant | `MY_CONST = value` |
| Access from outside | `ClassName::CONST` |
| Prevent mutation | `CONST = [...].freeze` |
| Nested namespacing | `Outer::Inner::CONST` |
| Subclass access | Inherited automatically |

**Key rule:** Constants in Ruby are about **scope and namespacing**, not true immutability — `freeze` is what gives you immutability.