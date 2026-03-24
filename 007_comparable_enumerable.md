# Comparable vs Enumerable in Ruby

---

## 🔹 Comparable

### 📌 Definition
`Comparable` is a module used to **compare objects**. It allows you to define ordering (`>`, `<`, `>=`, `<=`, `==`, `between?`).

👉 It requires you to implement the `<=>` operator (spaceship operator).

---

### ⚙️ How it works

The `<=>` method should return:
- `-1` → less than  
- `0` → equal  
- `1` → greater than  

---

### 🧠 Example
```ruby
class Student
  include Comparable
  attr_accessor :marks

  def initialize(marks)
    @marks = marks
  end

  def <=>(other)
    self.marks <=> other.marks
  end
end

s1 = Student.new(80)
s2 = Student.new(90)

puts s1 < s2             # true
puts s1 > s2             # false
puts s1.between?(70, 85) # true
```

---

### 🎯 Key Points
- Used for sorting and comparison
- Needs `<=>` method
- Provides: `<`, `>`, `<=`, `>=`, `==`, `between?`

---

## 🔹 Enumerable

### 📌 Definition
`Enumerable` is a module that provides iteration and collection methods like `map`, `select`, `reduce`, etc.

👉 It requires you to implement the `each` method.

---

### ⚙️ How it works
If your class defines `each`, then `Enumerable` gives you many powerful methods for free.

---

### 🧠 Example
```ruby
class MyCollection
  include Enumerable

  def initialize(items)
    @items = items
  end

  def each
    @items.each do |item|
      yield item
    end
  end
end

col = MyCollection.new([1, 2, 3, 4])

p col.map { |x| x * 2 }      # [2, 4, 6, 8]
p col.select { |x| x.even? } # [2, 4]
p col.reduce(:+)              # 10
```

---

### 🎯 Key Points
- Used for collections & iteration
- Needs `each` method
- Provides: `map`, `select`, `find`, `reduce`, `any?`, `all?`

---

## 🔥 Difference (Important for Interviews)

| Feature | Comparable | Enumerable |
|---|---|---|
| Purpose | Object comparison | Collection iteration |
| Required Method | `<=>` | `each` |
| Use Case | Sorting, ranking | Filtering, transforming |
| Example | Compare objects | Work with collections |

---

## 🧠 Easy Way to Remember
- **Comparable** → Compare objects
- **Enumerable** → Iterate collections
