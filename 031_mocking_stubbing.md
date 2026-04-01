# 🔹 Mocking vs Stubbing in Rails

Both **mocking** and **stubbing** are part of **test doubles** used in testing (especially with RSpec) to isolate behavior.

---

# ✅ 1. Stubbing (Focus: Return Value)

### 👉 Definition

**Stubbing** means replacing a method with a fake implementation that returns a predefined value.

👉 You don’t care *how* the method is called — only *what it returns*.

---

### 🧠 Simple Idea

> “When this method is called, just return this.”

---

### 💻 Example (RSpec)

```ruby
allow(User).to receive(:find).and_return(double(name: "Shyam"))

user = User.find(1)
puts user.name  # => "Shyam"
```

---

### 📌 Use Case

* Avoid hitting database
* Avoid calling external APIs
* Control test environment

---

### ⚡ Key Point

* No verification of method call
* Only controls output

---

# ✅ 2. Mocking (Focus: Behavior Verification)

### 👉 Definition

**Mocking** means setting an expectation that a method **must be called** during the test.

👉 You care about:

* Was the method called?
* How many times?
* With what arguments?

---

### 🧠 Simple Idea

> “This method should be called like this, otherwise test fails.”

---

### 💻 Example (RSpec)

```ruby
expect(NotificationService).to receive(:send_email).with("test@example.com")

NotificationService.send_email("test@example.com")
```

---

### ❌ If not called:

Test will fail.

---

### 📌 Use Case

* Verifying side effects
* Ensuring certain interactions happen (e.g., sending email, logging, jobs)

---

### ⚡ Key Point

* Verifies behavior (not just return value)

---

# 🔥 Key Differences

| Feature      | Stubbing                | Mocking                     |
| ------------ | ----------------------- | --------------------------- |
| Purpose      | Control return value    | Verify method call behavior |
| Verification | ❌ No                    | ✅ Yes                       |
| Test failure | Only if assertion fails | Fails if method not called  |
| Focus        | Output                  | Interaction                 |
| Syntax       | `allow(...).to receive` | `expect(...).to receive`    |

---

# 🎯 Real Rails Example

### Scenario: Payment Service

### 🔹 Using Stub

```ruby
allow(PaymentGateway).to receive(:charge).and_return(true)

result = PaymentGateway.charge(100)
expect(result).to be true
```

👉 We don’t care if it was called properly.

---

### 🔹 Using Mock

```ruby
expect(PaymentGateway).to receive(:charge).with(100)

PaymentGateway.charge(100)
```

👉 We care that it *must be called correctly*.

---

# 🧠 Easy Way to Remember

* **Stub = Fake Response**
* **Mock = Expect Behavior**

---

# 🚀 Interview Tip

👉 *When would you use mocking over stubbing?*

> “I use stubbing when I just want to control the return value and isolate dependencies.
> I use mocking when I want to verify that a specific interaction or side effect happens, like sending an email or calling an external service.”
