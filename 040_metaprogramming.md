# Metaprogramming in Ruby

## What is Metaprogramming?

**Metaprogramming** is the technique of writing code that can **inspect, generate, or modify other code at runtime**.

In Ruby, everything is an object, including classes and modules. That means Ruby code can manipulate structure and behavior while the program is running.

## Why use Metaprogramming?

- Remove repetitive boilerplate and keep code DRY.
- Build expressive DSLs and flexible APIs.
- Implement behavior based on runtime data.
- Support frameworks like Rails that generate methods and hooks dynamically.

## Core Ruby Patterns

### `define_method`

Use `define_method` to create methods dynamically.

```ruby
class Report
  [:daily, :weekly, :monthly].each do |frequency|
    define_method("generate_#{frequency}") do
      puts "Generating #{frequency} report"
    end
  end
end

Report.new.generate_daily  # => Generating daily report
```

### `send` / `public_send`

`send` invokes any method by name, including private methods. `public_send` restricts to public methods.

```ruby
object.send(:process)
object.public_send(:process)
```

### `method_missing` and `respond_to_missing?`

Handle unknown method calls dynamically and keep `respond_to?` consistent.

```ruby
class QueryBuilder
  def method_missing(name, *args, &block)
    if name.to_s.start_with?("find_by_")
      column = name.to_s.delete_prefix("find_by_")
      "SELECT * FROM users WHERE #{column} = #{args.first.inspect}"
    else
      super
    end
  end

  def respond_to_missing?(name, include_private = false)
    name.to_s.start_with?("find_by_") || super
  end
end

QueryBuilder.new.find_by_email("a@example.com")
```

### Singleton methods and eigenclass

Define behavior on one object only.

```ruby
user = Object.new
def user.display_name
  "Special user"
end
```

Or using the eigenclass:

```ruby
class << user
  def display_name
    "Special user"
  end
end
```

### `class_eval`, `module_eval`, and `instance_eval`

- `class_eval` / `module_eval` evaluate code in the context of a class or module.
- `instance_eval` evaluates code in the context of a specific object.

```ruby
class Person; end

Person.class_eval do
  attr_accessor :name
end

person = Person.new
person.instance_eval do
  self.name = "Alice"
end
```

### `attr_reader`, `attr_writer`, `attr_accessor`

These are metaprogramming helpers that generate getter/setter methods.

```ruby
class User
  attr_accessor :email
end
```

### `const_missing`

You can intercept missing constants and define them lazily.

```ruby
module Services
  def self.const_missing(name)
    const_set(name, Class.new)
  end
end

Services::EmailService # creates Services::EmailService dynamically
```

## Rails and Metaprogramming

Rails relies on Ruby metaprogramming heavily.

- Active Record associations: `has_many`, `belongs_to`
- Validations: `validates :name, presence: true`
- Dynamic attribute accessors from database columns
- Route and URL helper generation (`new_user_path`, `edit_post_path`)
- Callbacks and concerns that are added at runtime

## Use Cases

- Building DSLs: Rails migrations, route definitions, validations.
- Delegation / proxy objects.
- Dynamic dispatch based on method names or input.
- Generating boilerplate methods automatically.

## Advantages

- Eliminates repeated code.
- Makes APIs more concise and expressive.
- Allows behaviour to adapt dynamically at runtime.

## Disadvantages

- Can make code harder to read and debug.
- Increases cognitive load for maintainers.
- Can hide behavior from static analysis.
- Overuse may lead to fragile, hard-to-trace code.

## Interview Answer

> Metaprogramming in Ruby is the ability to write code that inspects, defines, or alters classes, modules, methods, and objects at runtime. Ruby offers tools like `define_method`, `send`, `method_missing`, `class_eval`, `instance_eval`, and singleton methods to support this. Rails uses metaprogramming throughout its API, including associations, validations, dynamic attributes, and route helpers.
