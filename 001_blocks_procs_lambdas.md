# Blocks, Procs, and Lambdas

---

## Q1. What is a Block in Ruby?

A block is an anonymous piece of code that can be passed to a method.  
It is not an object by itself, but it can be converted into a Proc object using `&block`.

It can be written with `{}` for single-line or `do...end` for multi-line.  
The method can execute it using `yield` or by converting it into a Proc.

### Basic Example

```ruby
def greet
  puts "----------Before the block----------"
  yield if block_given?
  puts "----------After the block----------"
end

# Without block
greet

# With block
greet do
  puts "Hello from the block!"
  puts "This is a great example!"
end
```

---

### Passing Arguments to a Block via yield

```ruby
def greet
  yield("Hello", "World") if block_given?
end

greet do |arg1, arg2|
  puts "#{arg1}, #{arg2}!"
end
```

---

### Capturing a Block with &block

```ruby
def greet(&block)
  block.call("Ruby") if block
end

greet { |name| puts "Hello, #{name}!" }
```

---

### Implicit vs Explicit Block

```ruby
def greet
  yield if block_given?   # implicit block
end

def greet(&block)
  block.call if block     # explicit block
end
```

- `yield` → faster, no object creation  
- `&block` → converts block into Proc (object)

---

## Q2. What is a Proc?

A `Proc` (short for "procedure") is an object that represents a block of code which can be stored, passed around, and executed later.

### Creating and Calling a Proc

```ruby
p = Proc.new { |x| puts x * 2 }

p.call(5)
p[6]
p.(7)
```

---

### Passing a Proc to a Method as a Block

```ruby
def greet
  puts "----------before------------"
  yield
  puts "----------after-------------"
end

my_proc = Proc.new { puts "I am a Proc" }
greet(&my_proc)
```

---

### Proc with Arguments

```ruby
def greet
  yield("Hello", "World")
end

my_proc = Proc.new do |arg1, arg2|
  puts "I am a Proc with args: #{arg1}, #{arg2}"
end

greet(&my_proc)
```

---

### Proc is Lenient with Arguments

Procs do not enforce argument count — missing args become `nil`, extra args are ignored.

```ruby
p = Proc.new { |x, y| puts "#{x}, #{y}" }

p.call(1)
p.call(1, 2, 3)
```

---

### Proc return Exits the Enclosing Method

```ruby
def test_proc
  p = Proc.new { return "From Proc" }
  p.call
  return "After Proc"
end

puts test_proc
```

> ⚠️ Proc `return` only works inside a method. If used at top level, it raises `LocalJumpError`.

---

## Q3. What is a Lambda?

A lambda is a special kind of `Proc` that behaves more like a method — it enforces argument count and its `return` only exits the lambda itself.

### Creating a Lambda

```ruby
my_lambda = lambda { |x| puts x * 2 }
my_lambda = ->(x) { puts x * 2 }
```

---

### Calling a Lambda

```ruby
add = ->(x, y) { x + y }

add.call(2, 3)
add[2, 3]
add.(2, 3)
```

---

### Lambda is Strict with Arguments

```ruby
l = ->(x, y) { puts "#{x}, #{y}" }

l.call(1)        # ArgumentError
l.call(1, 2, 3)  # ArgumentError
```

---

### Lambda return Only Exits the Lambda

```ruby
def test_lambda
  l = -> { return "From Lambda" }
  l.call
  return "After Lambda"
end

puts test_lambda
```

---

### Lambda is still a Proc

```ruby
l = -> {}
l.is_a?(Proc)  # => true
```

---

## Q4. Difference Between Block, Proc, and Lambda

| Feature | Block | Proc | Lambda |
|---|---|---|---|
| Is an object? | No | Yes | Yes |
| Store in variable? | No | Yes | Yes |
| `lambda?` check | N/A | false | true |
| `.class` | N/A | Proc | Proc |
| Argument strictness | N/A | Lenient | Strict |
| `return` behavior | N/A | Exits enclosing method | Exits lambda only |

```ruby
my_proc   = Proc.new { true }
my_lambda = -> { true }

my_proc.lambda?   # => false
my_lambda.lambda? # => true

my_proc.class     # => Proc
my_lambda.class   # => Proc
```

---

### Key Behavioral Difference

- Proc behaves like a loose block  
- Lambda behaves like a strict method  

Think of:
- Proc → flexible (ढीला)  
- Lambda → strict (सख्त, method-like)

---

## Q5. Bonus — `&method(:method_name)`

You can pass an existing method as a block using `&method(:name)`.

```ruby
def double(x)
  x * 2
end

[1, 2, 3].map(&method(:double))
```

Equivalent to:

```ruby
[1, 2, 3].map { |x| double(x) }
```

---

### Using Symbol to Proc Shortcut

```ruby
[1, -2, 3].select(&:positive?)
```

---

## Q6. Control Flow Keywords

- `return`
  - Proc → exits enclosing method
  - Lambda → exits only lambda

- `next`
  - Exits current block/lambda iteration

---

## Summary

- Use a **block** for one-time execution  
- Use a **Proc** for reusable, flexible logic  
- Use a **lambda** for strict, method-like behavior