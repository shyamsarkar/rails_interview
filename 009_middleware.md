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

## 🧠 Example (Custom Middleware)
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
