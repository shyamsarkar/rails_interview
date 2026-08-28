Q. Explain ActiveRecord's Dirty Tracking.

ActiveRecord Dirty Tracking lets you detect which attributes have changed on a model instance before it is saved to the database. Rails tracks the original value and the current value of each attribute so you can inspect, validate, or revert changes.

Common methods:
- `changed?` - returns true if any attribute changed
- `attribute_changed?` - checks one specific attribute
- `changed` - returns an array of changed attribute names
- `changes` - returns a hash like `{ "name" => ["Bob", "John"] }`
- `attribute_was` - returns the original value
- `attribute_change` - returns `[old_value, new_value]`
- `restore_attribute!` - restore a single attribute
- `restore_attributes` - restore multiple attributes

Example:

```ruby
user = User.find(1)
user.name = "John"
user.email = "john@example.com"

user.changed?          # => true
user.changed           # => ["name", "email"]
user.name_changed?     # => true
user.email_changed?    # => true

user.changes           # => { "name" => ["Bob", "John"], "email" => ["bob@example.com", "john@example.com"] }
user.name_was          # => "Bob"
user.name_change       # => ["Bob", "John"]
```

Why it matters:
- Useful before `save` to decide whether to persist updates
- Helps in callbacks, validations, and custom logic
- Allows you to inspect old vs new values without re-querying the database

After saving, dirty tracking resets:

```ruby
user.save
user.changed?          # => false
```

In short, dirty tracking is Rails' way of telling you: "This object has been modified, and here is exactly what changed."
