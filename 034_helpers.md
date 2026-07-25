# 🔶 Ruby on Rails Helpers

## What are Helpers?

**Helpers** are modules that contain **presentation logic** used by views.

Their purpose is to keep your **views (ERB/HAML)** clean by moving formatting and display-related code out of the template.

Instead of writing complex Ruby code inside your views, you create helper methods and call them from the view.

Helpers are automatically available in their corresponding views.

For example:

```
app/
├── helpers/
│   ├── application_helper.rb
│   ├── users_helper.rb
│   └── orders_helper.rb
```

---

# Why use Helpers?

Without helpers, your view may become cluttered.

### Without Helper

```erb
<p>
  <%= "#{@user.first_name.capitalize} #{@user.last_name.capitalize}" %>
</p>

<p>
  <%= @order.created_at.strftime("%d %b %Y") %>
</p>

<p>
  <%= number_to_currency(@order.total_price) %>
</p>
```

The view contains formatting logic, making it harder to read.

---

### With Helper

**app/helpers/users_helper.rb**

```ruby
module UsersHelper
  def full_name(user)
    "#{user.first_name.capitalize} #{user.last_name.capitalize}"
  end
end
```

**app/helpers/orders_helper.rb**

```ruby
module OrdersHelper
  def formatted_date(date)
    date.strftime("%d %b %Y")
  end

  def formatted_price(amount)
    number_to_currency(amount)
  end
end
```

Now your view becomes much cleaner:

```erb
<p><%= full_name(@user) %></p>

<p><%= formatted_date(@order.created_at) %></p>

<p><%= formatted_price(@order.total_price) %></p>
```

---

# Common Uses of Helpers

Helpers are commonly used for:

- Formatting dates
- Formatting currency
- Formatting phone numbers
- Formatting names
- Choosing CSS classes
- Generating HTML
- Displaying badges
- Creating links
- Display-related calculations

---

# Example 1: Date Formatting

```ruby
module ApplicationHelper
  def formatted_date(date)
    date.strftime("%d %b %Y")
  end
end
```

Usage:

```erb
<%= formatted_date(@user.created_at) %>
```

Output:

```
09 Jul 2026
```

---

# Example 2: Currency Formatting

```ruby
module OrdersHelper
  def formatted_price(amount)
    number_to_currency(amount)
  end
end
```

Usage:

```erb
<%= formatted_price(1250) %>
```

Output:

```
$1,250.00
```

---

# Example 3: Status Badge

```ruby
module UsersHelper
  def status_badge(user)
    css_class = user.active? ? "badge-success" : "badge-danger"

    content_tag(:span, user.status, class: css_class)
  end
end
```

Usage:

```erb
<%= status_badge(@user) %>
```

Output:

```html
<span class="badge-success">
  Active
</span>
```

---

# Example 4: CSS Class Helper

Instead of this in the view:

```erb
<div class="<%= @user.active? ? 'green' : 'red' %>">
```

Move it to a helper:

```ruby
def status_class(user)
  user.active? ? "green" : "red"
end
```

Now the view becomes:

```erb
<div class="<%= status_class(@user) %>">
```

---

# Example 5: Displaying Full Name

```ruby
module UsersHelper
  def full_name(user)
    "#{user.first_name} #{user.last_name}"
  end
end
```

Usage:

```erb
<%= full_name(@user) %>
```

---

# What Should NOT Go Into Helpers?

Helpers should **not** contain business logic.

❌ Bad Example

```ruby
def approve_order(order)
  order.update!(status: "approved")
end
```

This changes application data and belongs in the model or a service object—not a helper.

Helpers should only prepare data for display.

---

# Best Practices

✅ Keep helpers focused on presentation.

✅ Make helper methods small and reusable.

✅ Avoid database queries inside helpers.

✅ Don't put business logic in helpers.

---

# Interview Questions

### Q1. What are Helpers in Rails?

Helpers are Ruby modules used to extract presentation logic from views. They keep templates clean by moving formatting and display-related code into reusable methods.

---

### Q2. Where are Helpers stored?

```
app/helpers/
```

Examples:

```
application_helper.rb
users_helper.rb
orders_helper.rb
```

---

### Q3. When should you use a Helper?

Use a helper whenever you need to:

- Format data
- Generate HTML
- Choose CSS classes
- Format dates
- Format currency
- Display badges

---

### Q4. Should Helpers contain business logic?

No.

Helpers should only contain presentation logic.

Business logic belongs in models, concerns, or service objects.

---

# 🔶 Helpers vs Concerns

Although both Helpers and Concerns promote code reuse, they solve different problems.

| Feature | Helpers | Concerns |
|---------|---------|----------|
| Purpose | Reuse presentation logic | Reuse business logic |
| Used In | Views | Models and Controllers |
| Directory | `app/helpers/` | `app/models/concerns/` or `app/controllers/concerns/` |
| Main Responsibility | Display and formatting | Shared application behavior |
| Access Database | Generally no | Yes |
| Can define callbacks/scopes? | No | Yes |
| Uses `ActiveSupport::Concern`? | No | Yes |

---

# Example

Suppose your application displays product prices.

### Helper

```ruby
module ProductsHelper
  def formatted_price(product)
    number_to_currency(product.price)
  end
end
```

Usage:

```erb
<%= formatted_price(@product) %>
```

The helper only changes how the price is displayed.

---

Now suppose several models can be published.

### Concern

```ruby
module Publishable
  extend ActiveSupport::Concern

  included do
    scope :published, -> { where(published: true) }
  end

  def publish!
    update!(published: true)
  end

  def published?
    published
  end
end
```

Usage:

```ruby
class Article < ApplicationRecord
  include Publishable
end

class Blog < ApplicationRecord
  include Publishable
end
```

The concern adds reusable behavior to multiple models.

---

# Simple Rule to Remember

Ask yourself:

> **"Does this change how the data is displayed?"**

✅ Use a **Helper**

Examples:

- Format dates
- Format prices
- Format names
- Generate HTML
- Choose CSS classes

---

Ask yourself:

> **"Does this change how the application behaves?"**

✅ Use a **Concern**

Examples:

- Publish records
- Archive records
- Authentication
- Shared validations
- Shared scopes
- Shared callbacks
- Shared associations

---

# Interview Answer

### What is the difference between Helpers and Concerns?

Helpers are used to extract **presentation logic** from views. They make templates cleaner by handling formatting, HTML generation, and display-related tasks.

Concerns are used to extract **shared business logic** from models or controllers. They allow multiple classes to share methods, scopes, callbacks, validations, and associations using `ActiveSupport::Concern`.

In short:

- **Helper = "How should it look?"**
- **Concern = "How should it behave?"**

---

# Key Takeaways

- Helpers keep views clean.
- Helpers are for presentation logic.
- Concerns keep models and controllers DRY.
- Concerns are for shared business logic.
- Never put business logic inside helpers.
- Use concerns when multiple models or controllers share the same behavior.
```