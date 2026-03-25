# Q. What is Polymorphic Association and STI (Single Table Inheritance) in Rails?

---

## Polymorphic Associations

Polymorphic associations allow a model to belong to more than one other model using a single association.

Instead of having multiple foreign keys, Rails uses:
- `<name>_id`
- `<name>_type`

### Example
```ruby
# A Comment can belong to either a Post or a Photo
class Comment < ApplicationRecord
  belongs_to :commentable, polymorphic: true
end

class Post < ApplicationRecord
  has_many :comments, as: :commentable
end

class Photo < ApplicationRecord
  has_many :comments, as: :commentable
end
```

### Database schema
```ruby
create_table :comments do |t|
  t.text :content
  t.references :commentable, polymorphic: true  # creates commentable_id & commentable_type
  t.timestamps
end
```

### Key points

- A single model can be associated with multiple models.
- Uses `*_id` and `*_type` columns.
- Useful for flexible relationships (e.g., comments, likes, attachments).

---

## Single Table Inheritance (STI)

STI allows multiple models to be stored in a single database table using a `type` column.
All subclasses inherit from a base class and share the same table.

### Example
```ruby
# Base class
class Vehicle < ApplicationRecord
end

class Car < Vehicle
  def doors
    4
  end
end

class Motorcycle < Vehicle
  def wheels
    2
  end
end

class Truck < Vehicle
  def cargo_capacity
    payload_weight
  end
end
```

### Database schema
```ruby
create_table :vehicles do |t|
  t.string :type         # used by Rails for STI
  t.string :make
  t.string :model
  t.integer :year
  t.integer :payload_weight
  t.timestamps
end
```

### Key points

- All records are stored in one table.
- `type` column determines the subclass.
- Reduces table count but can lead to many unused columns.

---

## Polymorphic vs STI — difference

| Feature       | Polymorphic Association                  | STI                            |
|---------------|------------------------------------------|--------------------------------|
| Purpose       | Flexible relationships between models    | Inheritance between models     |
| Tables        | Multiple tables                          | Single table                   |
| Key columns   | `*_id`, `*_type`                         | `type`                         |
| Use case      | Comment on Post, Photo, etc.             | Car, Truck, Bike as Vehicle    |

---

## Interview tip

- Use **Polymorphic** when one model relates to multiple unrelated models.
- Use **STI** when models share common behavior and structure.