# CORS and CSRF

---

## Q. What is CORS?

**Answer:**

CORS (Cross-Origin Resource Sharing) is a security mechanism implemented by browsers that allows or restricts web pages from making requests to a different domain than the one that served the web page.

👉 It is controlled by HTTP headers sent by the server.

### Example:
If your frontend is running on:
- http://frontend.com

And backend is:
- http://api.com

👉 Browser blocks request unless CORS is enabled on the server.

### In Rails:

```ruby
# Gemfile
gem 'rack-cors'
```

```ruby
# config/initializers/cors.rb
Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins '*'
    resource '*',
      headers: :any,
      methods: [:get, :post, :put, :delete]
  end
end
```

---

## Q. What is CSRF?

**Answer:**

CSRF (Cross-Site Request Forgery) is an attack where a malicious website tricks a user into performing unwanted actions on a trusted site where the user is authenticated.

👉 It exploits the fact that browsers automatically send cookies with requests.

### Example:
User logged into bank.com → visits malicious site → request sent to bank.com without user consent.

---

## 🔐 How Rails Prevents CSRF?

Rails uses **authenticity tokens**.

```ruby
protect_from_forgery with: :exception
```

- A unique token is generated per session
- Must be included in forms/requests
- Server verifies the token

---

## ⚖️ Difference Between CORS and CSRF

| Feature | CORS | CSRF |
|--------|------|------|
| Type | Browser security policy | Security attack |
| Purpose | Controls cross-origin requests | Prevents unauthorized actions |
| Controlled by | Server headers | Tokens |
| Happens when | Different domains | User is authenticated |

---

## 🧠 One-Line Memory

- **CORS** → "Who can request?"  

- **CSRF** → "Is request valid or not?"  