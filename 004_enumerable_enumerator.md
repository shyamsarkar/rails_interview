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

### Creating a Custom Enumerator

You can create an `Enumerator` by passing a block. The block receives a
**yielder** object, which sends values to the enumerator with `y << value`.

```ruby
e = Enumerator.new do |y|
	a = 0
	loop do
		y << a
		a += 1
	end
end

e.next # => 0
e.next # => 1
e.next # => 2
```
## Iterators and Generators

An **iterator** is an object or method that moves through a sequence one value
at a time. each, map, select, reject, each_with_index, find, reduce, group_by — all of these are iterators
They're the methods Enumerable gives you, and they all iterate.
In Ruby, methods such as `each` act as iterators when they yield
each element of a collection:

```ruby
numbers = [1, 2, 3]

numbers.each do |number|
	puts number
end
```

A **generator** produces values on demand instead of creating the complete
sequence up front. It can pause after producing a value and resume when the
next value is requested. Ruby does not have a separate `generator` keyword;
an `Enumerator` is commonly used to implement generator-like behavior:

```ruby
fibonacci = Enumerator.new do |y|
	first = 0
	second = 1

	loop do
		y << first
		first, second = second, first + second
	end
end

fibonacci.take(6) # => [0, 1, 1, 2, 3, 5]
```

### Iterator vs Generator

| Iterator | Generator |
|----------|-----------|
| Traverses or yields values from a sequence | Produces values, often lazily and on demand |
| Commonly works with an existing collection | Can create an infinite or computed sequence |
| Example: `array.each` | Example: `Enumerator.new` |

> **In simple words:** An iterator consumes a sequence one item at a time,
> while a generator creates or supplies the next item only when requested.
```

This is an infinite enumerator, so calling `e.to_a` would never finish. Use
`take` when consuming it as a finite collection:

```ruby
e.rewind
e.take(5) # => [0, 1, 2, 3, 4]
```

The enumerator computes each value on demand and pauses at `y << a` until the
next value is requested.

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