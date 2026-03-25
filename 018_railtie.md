## Railtie

Railtie is the core extension mechanism in Ruby on Rails that allows gems or components to hook into the Rails boot process.

### ✅ Key Points

* Used to extend Rails
* Runs during application startup
* Allows adding:
  * Initializers
  * Rake tasks
  * Middleware

### 🧾 Example
```ruby
class MyGem::Railtie < Rails::Railtie
  initializer "my_gem.init" do
    puts "MyGem loaded"
  end
end
```

### 🧠 One-Line Memory

👉 **Railtie** = Entry point for gems to integrate with Rails