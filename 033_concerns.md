# 🔶 Ruby on Rails Concerns (Interview Notes)

## What are Concerns?

A **Concern** is a way to **extract shared code** from multiple models or controllers into a reusable module.

Instead of duplicating the same methods across several classes, Rails allows us to place them inside a **Concern** and include them wherever needed.

Rails provides `ActiveSupport::Concern`, which makes creating reusable modules easier.

---

# Why use Concerns?

Without concerns:

```ruby
class User < ApplicationRecord
  def active?
    status == "active"
  end
end

class Admin < ApplicationRecord
  def active?
    status == "active"
  end
end

class Customer < ApplicationRecord
  def active?
    status == "active"
  end
end
```

The same method is duplicated in multiple models.

With a concern:

```ruby
module Statusable
  extend ActiveSupport::Concern

  def active?
    status == "active"
  end
end
```

Now simply include it:

```ruby
class User < ApplicationRecord
  include Statusable
end

class Admin < ApplicationRecord
  include Statusable
end

class Customer < ApplicationRecord
  include Statusable
end
```

Now the code exists in only one place.

---

# Directory Structure

Model concerns:

```
app/
└── models/
    └── concerns/
        └── statusable.rb
```

Controller concerns:

```
app/
└── controllers/
    └── concerns/
        └── authenticatable.rb
```

Rails automatically autoloads these folders.

---

# Basic Concern Syntax

```ruby
module Statusable
  extend ActiveSupport::Concern

  def active?
    status == "active"
  end
end
```

Usage:

```ruby
class User < ApplicationRecord
  include Statusable
end
```

---

# Using `included do`

Sometimes you don't want only methods—you also want to add validations, callbacks, scopes, or associations.

Example:

```ruby
module Statusable
  extend ActiveSupport::Concern

  included do
    scope :active, -> { where(status: "active") }

    validates :status, presence: true
  end

  def active?
    status == "active"
  end
end
```

Now every model including this concern automatically gets:

- Validation
- Scope
- Instance method

---

# Adding Class Methods

```ruby
module Publishable
  extend ActiveSupport::Concern

  class_methods do
    def published
      where(published: true)
    end
  end
end
```

Usage:

```ruby
Article.published
```

---

# Complete Example

```ruby
# app/models/concerns/publishable.rb

module Publishable
  extend ActiveSupport::Concern

  included do
    scope :published, -> { where(published: true) }
  end

  def publish!
    update!(published: true)
  end

  class_methods do
    def recent
      order(created_at: :desc)
    end
  end
end
```

Include it:

```ruby
class Article < ApplicationRecord
  include Publishable
end
```

Usage:

```ruby
Article.published
Article.recent

article.publish!
```

---

# Controller Concern Example

```ruby
module Authenticatable
  extend ActiveSupport::Concern

  included do
    before_action :authenticate_user!
  end

  private

  def authenticate_user!
    redirect_to login_path unless current_user
  end
end
```

Usage:

```ruby
class OrdersController < ApplicationController
  include Authenticatable
end
```

Now every action is automatically authenticated.

---

# Common Uses of Concerns

Model concerns:

- Status methods
- Soft delete logic
- Search methods
- Slug generation
- Auditing
- Publish/Archive functionality
- Timestamp helpers
- Shared validations

Controller concerns:

- Authentication
- Authorization
- Pagination
- Error handling
- API response formatting
- Rate limiting
- Logging

---

# Concern vs Service Object

| Concern | Service Object |
|----------|----------------|
| Shares behavior | Performs business logic |
| Mixed into models/controllers | Standalone class |
| Reusable methods | Executes one operation |
| Adds methods to a class | Doesn't modify models |
| Good for common functionality | Good for workflows |

Example:

Concern:

```ruby
module Publishable
  def publish!
    update!(published: true)
  end
end
```

Service Object:

```ruby
class PublishArticle
  def self.call(article)
    article.update!(published: true)
    NotificationMailer.article_published(article).deliver_now
  end
end
```

---

# Concern vs Helper

| Concern | Helper |
|----------|--------|
| Used in models/controllers | Used in views |
| Business logic | Presentation logic |

---

# Best Practices

✅ Keep concerns focused on a single responsibility.

✅ Use concerns only when code is shared by multiple classes.

✅ Name concerns clearly (`Publishable`, `Searchable`, `Taggable`).

✅ Avoid creating very large concerns.

✅ If a concern starts containing complex workflows, consider using a Service Object instead.

---

# Common Mistakes

### ❌ Putting unrelated methods together

Bad:

```ruby
module Utilities
  def active?; end

  def send_email; end

  def calculate_tax; end
end
```

Good:

```
Statusable
Taxable
Emailable
```

---

### ❌ Using concerns for business processes

Bad:

```ruby
module PaymentConcern
  def process_payment
    # 200 lines of code
  end
end
```

Better:

```ruby
class PaymentProcessor
  def self.call(order)
    # Business logic
  end
end
```

---

# Interview Questions

### Q1. What is a Concern in Rails?

A Concern is a reusable module that allows shared code to be extracted from models or controllers using `ActiveSupport::Concern`, helping reduce duplication and improve organization.

---

### Q2. Why use `ActiveSupport::Concern` instead of a plain Ruby module?

`ActiveSupport::Concern` provides a cleaner syntax for:
- `included do` blocks
- `class_methods`
- Managing module dependencies

Without it, you'd need to manually define `self.included(base)` and extend class methods.

---

### Q3. What can be included inside a Concern?

- Instance methods
- Class methods
- Scopes
- Validations
- Callbacks
- Associations
- Constants

---

### Q4. When should you avoid using Concerns?

Avoid concerns when:
- The code is used by only one class.
- The logic represents a business workflow (use a Service Object).
- The concern becomes too large or handles multiple responsibilities.

---

# Key Takeaways

- A Concern extracts shared behavior into reusable modules.
- `ActiveSupport::Concern` simplifies module creation.
- Concerns can define instance methods, class methods, scopes, callbacks, and validations.
- Use concerns for shared behavior, not for complex business workflows.
- Keep concerns small, focused, and reusable.