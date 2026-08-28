Q. Explain shallow copy and deep copy.

A shallow copy creates a new top-level object, but keeps the same nested objects as the original. In Ruby, `dup` is a shallow copy. This means the outer hash is duplicated, but any nested hash or array is still shared between the original and the copy.

A deep copy creates a fully independent copy of the object and all nested objects. In Ruby, a common way to do this is:

```ruby
original = { name: "John", address: { city: "NYC", zip: "10001" } }

shallow_copy = original.dup
deep_copy = Marshal.load(Marshal.dump(original))
```

Example:

```ruby
original = { name: "John", address: { city: "NYC", zip: "10001" } }

shallow_copy = original.dup
shallow_copy[:name] = "Jane"
shallow_copy[:address][:city] = "Boston"

p original
# => { name: "John", address: { city: "Boston", zip: "10001" } }
```

Here, `shallow_copy[:address]` still points to the same nested hash as `original[:address]`, so modifying the nested object affects both.

```ruby
original = { name: "John", address: { city: "NYC", zip: "10001" } }

deep_copy = Marshal.load(Marshal.dump(original))
deep_copy[:address][:city] = "Chicago"

p original
# => { name: "John", address: { city: "NYC", zip: "10001" } }
```

Now `deep_copy` has its own nested `address` hash, so changes to one do not affect the other.

Important points:
- `dup` is shallow, not deep.
- `clone` is also shallow in the same way.
- Deep copy is useful when nested data must be modified independently.
- In Ruby, deep copy via `Marshal` works for plain data structures but may not work for every object type or may be slower and less safe for complex objects.

In short:
- Shallow copy: duplicate the outer object only.
- Deep copy: duplicate the entire object graph recursively.