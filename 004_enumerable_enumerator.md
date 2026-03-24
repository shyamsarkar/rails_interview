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
[1, 2, 3].map { |n| n * 2 }
# => [2, 4, 6]