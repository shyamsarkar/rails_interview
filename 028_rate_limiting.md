# 🚀 Rate Limiting in Rails

Rate limiting is a way to **control how many requests a client (user/IP/API key) can make within a certain time window**. It helps protect your app from abuse, bots, and overload.

---

## 🎯 Why Rate Limiting is Important

- Prevent **DDoS attacks**
- Stop **brute-force login attempts**
- Avoid **API misuse**
- Maintain **system stability**

---

## 🧠 Basic Idea

Define a rule like:

> "Allow max 100 requests per user per minute"

If the limit is exceeded → block or throttle the request.

---

## 🛠️ Implementation in Rails

### 1. Using `rack-attack` (Most Common)

#### Installation
```ruby
# Gemfile
gem 'rack-attack'
```
```bash
bundle install
```

### 2. Configuration
```ruby
# config/initializers/rack_attack.rb
class Rack::Attack
  # Throttle requests by IP
  throttle('req/ip', limit: 100, period: 1.minute) do |req|
    req.ip
  end

  # Throttle login attempts (prevent brute force)
  throttle('logins/ip', limit: 5, period: 20.seconds) do |req|
    if req.path == '/login' && req.post?
      req.ip
    end
  end
end
```

### 3. Custom Response
```ruby
Rack::Attack.throttled_response = lambda do |env|
  [429, {}, ["Too many requests. Please try later."]]
end
```

---

## ⚡ Storage (Important)

Rate limiting needs fast storage to track request counts:

- ❌ Memory (not scalable)
- ❌ File store (slow)
- ✅ **Redis (Recommended)**
```ruby
# config/environments/production.rb
config.cache_store = :redis_cache_store, { url: ENV['REDIS_URL'] }
```

---

## 🧩 Rate Limiting Strategies

### 1. Fixed Window
- Example: 100 requests per minute
- ❌ Issue: burst at boundary

### 2. Sliding Window
- More accurate
- Tracks requests continuously over time

### 3. Token Bucket (Advanced)
- Requests consume tokens
- Tokens refill over time
- Allows controlled bursts

---

## 🎯 Real-World Use Cases

- Login API → 5 attempts per minute
- OTP API → 3 requests per 10 minutes
- Public API → 1000 requests/hour per API key

---

## ⚠️ Common Mistakes

- ❌ Using only IP (NAT users share IP)
- ❌ Not using Redis
- ❌ Blocking instead of throttling
- ❌ Not logging violations

---

## 🧪 Advanced Example (Per User)
```ruby
throttle('api/user', limit: 200, period: 1.minute) do |req|
  req.env['current_user']&.id
end
```