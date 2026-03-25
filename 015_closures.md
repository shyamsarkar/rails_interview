# What are closures in Ruby?

A closure is a function-like object (`Proc` or `Lambda`) that captures and remembers the variables from the scope in which it was defined, even after that scope has finished executing.

In Ruby, closures are implemented using **Procs** and **Lambdas**.

---

## Example — Proc closure
```ruby
def counter
  count = 0
  return Proc.new { count += 1 }
end

c = counter
c.call # => 1
c.call # => 2
c.call # => 3
```

The Proc captures `count` — even though `counter` has returned, the Proc still reads and updates it.
Each call to `counter` creates a fresh closure with its own independent `count`.

---

## Same behaviour with a lambda
```ruby
def counter
  count = 0
  -> { count += 1 }
end
```

---

## Key points

- Closures capture **variables**, not just values.
- They preserve state across multiple calls.
- Commonly used for encapsulation, callbacks, and functional-style programming.

---

## Deeper (very important)

> Closures capture variables **by reference**, not by value. That's why the variable keeps updating across calls.

---

## Bonus — Real-world use case

A rate limiter using a lambda closure:
```ruby
def rate_limiter(limit)
  count = 0
  -> {
    count += 1
    count <= limit
  }
end

check = rate_limiter(3)

check.call # => true
check.call # => true
check.call # => true
check.call # => false
```