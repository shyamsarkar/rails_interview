## Q. Difference between Enumerable and Enumerator

### Enumerable

**Enumerable** is a module that provides a collection of **iteration and searching methods**.

It is included in classes like `Array`, `Hash`, and `Set`, allowing them to use powerful methods such as:
- `each`
- `map`
- `select`
- `reject`
- `reduce`

#### Key Points
- Requires the class to implement `each`
- Provides **higher-order methods** for collection processing
- Used for **internal iteration (अंदर से iteration)**

#### Example
```ruby
[1, 2, 3].map { |n| n * 2 } # => [2, 4, 6]
```

### Enumerator in Ruby

An **Enumerator** is a Ruby object that represents an iteration. It lets you traverse a collection one element at a time, manually control the iteration, or perform lazy evaluation.

> **In simple words:** An Enumerator stores **how to iterate**, not the data itself.

---

#### Explanation

Normally, methods like `each` immediately iterate when a block is given.

```ruby
[1, 2, 3].each { |n| puts n }
```

If no block is given, Ruby returns an `Enumerator` instead.

```ruby
enum = [1, 2, 3].each

puts enum.class
# Enumerator
```

You can then manually retrieve elements:

```ruby
puts enum.next   # 1
puts enum.next   # 2
puts enum.next   # 3
```

---

## Why use Enumerator?

- Manually control iteration using `next`.
- Pause and resume iteration.
- Process large datasets efficiently.
- Create lazy (on-demand) sequences.
- Build custom iterators.

---

## Common Methods

| Method | Description |
|---------|-------------|
| `next` | Returns the next element |
| `peek` | Views the next element without moving forward |
| `rewind` | Resets the iterator |
| `lazy` | Creates a lazy enumerator |
| `with_index` | Adds an index during iteration |

---

## Enumerator vs Enumerable

| Enumerator | Enumerable |
|------------|------------|
| Class | Module |
| Controls iteration | Provides iteration methods like `map`, `select`, and `find` |
| Returned by methods like `each` when no block is given | Included in classes like `Array`, `Hash`, and `Range` |

---

## Interview Definition

> **Enumerator is a Ruby class that represents an iteration. It allows you to manually control traversal of a collection, pause and resume iteration, and perform lazy evaluation. Methods like `each` return an Enumerator when called without a block.**