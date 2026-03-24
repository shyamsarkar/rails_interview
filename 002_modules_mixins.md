# Modules & Mixins

---

## Q. Explain modules in Ruby and how they're used.

A module is a collection of methods, constants, and classes.

It serves two main purposes:

**1. Namespacing** - To organize code and avoid naming conflicts.

```ruby
module Reports
  class Daily; end
end

Reports::Daily.new
```

**2. Mixins (Code Reuse)** - by using `include`, `prepend` and `extend`.

---

## Q. What are mixins?

Mixins are a powerful mechanism for sharing functionality between classes.
They are achieved by using modules and the keywords `include`, `prepend`, and `extend`.

---

## Q. How do you add both instance and class methods from a module?

```ruby
module Trackable
  def self.included(base)
    base.extend ClassMethods
  end

  module ClassMethods
    def track_all
      "Tracking all records"
    end
  end

  def track
    "Tracking instance"
  end
end

class User
  include Trackable
end

User.track_all     # => "Tracking all records"
User.new.track     # => "Tracking instance"
```

---

## Q. Explain `include` (instance method), `prepend` (instance method with priority) and `extend` (class method).

### `include` — Adds methods as instance methods

Its methods become instance methods of the class. The module is inserted into the ancestor chain **above** the class. Can be overwritten by the same class method.

```ruby
module Greet
  def hello
    "Hello from Greet"
  end
end

class Person
  include Greet
end

class Human
  include Greet
  def hello
    "I'm Human method"
  end
end

p = Person.new
p.hello  # => "Hello from Greet"

h = Human.new
h.hello  # => "I'm Human method"
```

---

### `prepend` — Adds methods as instance methods (with priority)

Similar to `include`, but the module is inserted into the ancestor chain **below** the class. This means module methods take precedence over class methods.

```ruby
class Person
  prepend Greet
end

class Human
  prepend Greet
  def hello
    "I'm Human method"
  end
end

p = Person.new
p.hello  # => "Hello from Greet"

h = Human.new
h.hello  # => "Hello from Greet"
```

---

### `extend` — Adds methods as class methods

Its methods become class methods (or singleton methods on that specific object).
- Adds the module's methods as singleton methods to a specific object or class, not to instances.
- Typically used to add class methods.

```ruby
module Greet
  def hello
    "Hello from module"
  end
end

class Person
  extend Greet
end

Person.hello       # => "Hello from module"  (class method)
person = Person.new
person.hello       # => NoMethodError        (not an instance method)
```

---

### Summary: `.ancestors` and the lookup chain

`.ancestors` shows the lookup chain for instance methods of a class.

```ruby
module A
end

module B
end

class MyClass
  include A
  prepend B
end

puts MyClass.ancestors  # => [B, MyClass, A, Object, Kernel, BasicObject]
```

- **B** comes before `MyClass` → because we used `prepend B`
- **A** comes after `MyClass` → because we used `include A`

---

### What about `extend`?

`extend` does **not** affect `.ancestors`, because it adds singleton methods to the class itself, not instance methods.

```ruby
module C
end

class MyClass
  extend C
end

puts MyClass.ancestors  # => [MyClass, Object, Kernel, BasicObject]
```

But you can still see that `C` was added to `MyClass`'s singleton class:

```ruby
puts MyClass.singleton_class.ancestors
# => [#<Class:MyClass>, C, #<Class:Object>, #<Class:BasicObject>]
```