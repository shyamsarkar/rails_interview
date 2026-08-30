# Middleware in Ruby (Rails)

---

## 🔹 What is Middleware?

Middleware is a layer that sits **between the request and the application**.

👉 It processes the request **before it reaches the controller** and can also modify the response **before it goes back to the user**.

👉 Think of it like a **filter / pipeline**.

---

## 🔄 Flow
```text
Request → Middleware → Controller → Response → Middleware → User
```

---

## 🔹 Why Middleware is Used?
- Logging requests
- Authentication
- Error handling
- Modifying request/response
- Performance tracking

---

## 🔹 How Middleware Works

Each middleware:
- Receives request
- Can modify it
- Passes it to next middleware
- Gets response back
- Can modify response

---

## � Rails Middleware Stack API

In Rails, `config.middleware` is an instance of `ActionDispatch::MiddlewareStack`.

It behaves like a stack of middleware objects that are assembled into the final Rack app.

### 1) Adding / inserting

- `use(klass, *args, &block)` — append to the end (innermost, right before the app)
- `unshift(klass, *args, &block)` — prepend to the front (outermost)
- `insert(index, klass, *args, &block)` — insert at a given position
- `insert_before(index, klass, *args, &block)` — alias for `insert`
- `insert_after(index, *args, &block)` — insert just after a given middleware

### 2) Replacing / removing

- `swap(target, *args, &block)` — replace one middleware with another
- `delete(target)` — remove; returns `nil` if not found
- `delete!(target)` — remove; raises `RuntimeError` if not found

### 3) Reordering

- `move(target, source)` — move `source` to before `target`
- `move_before(target, source)` — alias for `move`
- `move_after(target, source)` — move `source` to after `target`

### 4) Reading / introspection

- `[]` — index access into the stack
- `each` — iterate over middlewares
- `last` — the last (innermost) middleware
- `size` — number of middlewares
- `build` — assembles the final Rack app from the stack
- `initialize_copy` — deep-copies the stack (used internally for `dup`)
- `new` — constructor

This is useful because Rails lets you modify the middleware chain at startup time, often in `config/application.rb` or environment files.

---

## �🧠 Example (Custom Middleware)
```ruby
class LoggerMiddleware
  def initialize(app)
    @app = app
  end

  def call(env)
    start_time = Time.now
    status, headers, response = @app.call(env)
    end_time = Time.now
    duration = end_time - start_time

    method = env["REQUEST_METHOD"]
    path = env["PATH_INFO"]
    Rails.logger.info "Path #{method} #{path} Time: #{duration} seconds"

    [status, headers, response]
  end
end
```

---

## 🔹 Adding Middleware in Rails
```ruby
# config/application.rb
require_relative "../lib/middleware/time_logger"
config.middleware.use LoggerMiddleware
```

---

## 🔹 Common Built-in Middleware in Rails

| Middleware | Purpose |
|---|---|
| `Rack::Logger` | Logs requests |
| `Rack::Runtime` | Tracks request time |
| `ActionDispatch::Session` | Manages sessions |
