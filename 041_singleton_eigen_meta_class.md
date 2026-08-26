# Singleton Class, Eigenclass, and Metaclass in Ruby

A **singleton class** (also called an **eigenclass**) is a hidden class that stores methods for one specific object.

```ruby
user = Object.new

def user.admin?
  true
end

user.admin? # => true
```

Other objects are unaffected. The `class << object` syntax also opens that object's singleton class.

Because classes are objects too, they have singleton classes. A class method such as `def self.find` is a singleton method on the class object, stored in its singleton class.

```ruby
class User
  def self.find(id)
    "User #{id}"
  end
end

class Admin < User; end
Admin.find(1) # => "User 1"
```

The singleton class of `User` follows the class inheritance chain, which is why `Admin` inherits `User`'s class methods. It is itself a `Class`:

```ruby
User.singleton_class.is_a?(Class) # => true
```

**Metaclass** is an older term for the singleton class of a class object. In modern Ruby, use `singleton_class` for all three cases.

### Interview answer

> A singleton class is an object's hidden, per-object class. It lets Ruby define methods on only one object. An eigenclass is the same thing. Class methods are singleton methods on class objects, and subclasses can inherit them through the singleton-class inheritance chain. Metaclass is the older term for a class object's singleton class.

> This is different from Ruby's `Singleton` module, which enforces one instance of a class through `.instance`.

### Singleton design pattern

Ruby's standard library provides the `Singleton` module to ensure that a class has only one instance:

```ruby
require "singleton"

class AppConfig
  include Singleton
end

AppConfig.instance.equal?(AppConfig.instance) # => true
AppConfig.new # => NoMethodError (`new` is private)
```

This is the **Singleton design pattern**, not a singleton class. The module makes `new` private and provides the shared object through `.instance`.