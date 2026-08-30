# Value Objects

A value object is an object whose meaning is defined by its value, not by its identity.

Two value objects with the same data are considered equal, even if they are different instances.

## Key characteristics

- Immutable or effectively immutable
- Equality is based on value, not object identity
- Represents a domain concept rather than an entity
- Usually does not have its own database row or lifecycle
- Encapsulates behavior and validation

## Example

```ruby
class Money
  include Comparable

  attr_reader :amount, :currency

  def initialize(amount, currency)
    @amount = amount
    @currency = currency
  end

  def ==(other)
    other.is_a?(Money) && amount == other.amount && currency == other.currency
  end

  def +(other)
    raise ArgumentError, "Different currencies" unless currency == other.currency
    Money.new(amount + other.amount, currency)
  end
end
```

```ruby
Money.new(10, "USD") == Money.new(10, "USD")
# => true
```

## Why they are useful

- Better domain modeling
- Prevents primitive obsession
- Makes invalid states harder to represent
- Keeps business rules close to the data
- Improves readability and maintainability

## Difference from entities

An entity has identity and lifecycle. Example: `User`, `Order`, `Product`.

A value object is a descriptive value. Example: `Money`, `EmailAddress`, `Address`, `DateRange`.

## Rails example

```ruby
class EmailAddress
  def initialize(value)
    @value = value
    raise ArgumentError, "Invalid email" unless value.match?(/\A.+@.+\..+\z/)
  end

  def to_s
    @value
  end

  def ==(other)
    other.is_a?(EmailAddress) && @value == other.to_s
  end
end
```

## Interview answer

> A value object is a small immutable object whose identity is irrelevant; only its value matters. Two value objects with the same attributes are considered equal, and they are typically used to model domain concepts like money, email addresses, or addresses.

## One-line summary

Value object = a meaningful, immutable value with behavior, not a record with its own identity.
