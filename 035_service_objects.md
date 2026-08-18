# ❓ What are Service Objects in Rails?
# ❓ When Should You Use Service Objects?
# ❓ How Do You Implement Service Objects?

---

## 🧠 Answer

### 🔹 What is a Service Object?

A **Service Object** is a Plain Old Ruby Object (PORO) that encapsulates a specific business operation or process.

👉 It's a way to **move complex business logic** out of models and controllers into a dedicated, reusable object.

### 🎯 Key Characteristics:
- **Single responsibility** - Does one thing well
- **Reusable** - Can be called from multiple places
- **Testable** - Easy to unit test in isolation
- **Plain Ruby** - No inheritance from Rails classes needed

---

## 🚫 Problem: Why Do We Need Service Objects?

### 😫 Without Service Objects (Fat Model)

```ruby
class User < ApplicationRecord
  def register(name, email, password, company_name)
    # Create user
    save!

    # Create company
    company = Company.create!(name: company_name)
    update(company_id: company.id)

    # Send welcome email
    UserMailer.welcome(self).deliver_later

    # Send SMS
    SmsService.notify("Welcome #{name}!")

    # Create audit log
    AuditLog.create!(action: "user_registered", user_id: id)

    # Send to analytics
    Analytics.track("user_signup", user_id: id)

    self
  end
end
```

### ❌ Problems:
- Model becomes cluttered
- Multiple responsibilities in one method
- Hard to test (needs to mock multiple services)
- Difficult to reuse this logic
- Hard to change one part without affecting others

---

### ✅ With Service Objects

```ruby
class UserRegistrationService
  def initialize(name, email, password, company_name)
    @name = name
    @email = email
    @password = password
    @company_name = company_name
  end

  def call
    create_user
    create_company
    send_welcome_email
    send_sms_notification
    create_audit_log
    track_analytics

    user
  end

  private

  attr_reader :name, :email, :password, :company_name
  attr_accessor :user

  def create_user
    @user = User.create!(
      name: name,
      email: email,
      password: password
    )
  end

  def create_company
    company = Company.create!(name: company_name)
    user.update(company_id: company.id)
  end

  def send_welcome_email
    UserMailer.welcome(user).deliver_later
  end

  def send_sms_notification
    SmsService.notify("Welcome #{name}!")
  end

  def create_audit_log
    AuditLog.create!(action: "user_registered", user_id: user.id)
  end

  def track_analytics
    Analytics.track("user_signup", user_id: user.id)
  end
end
```

#### Usage in Controller:
```ruby
class UsersController < ApplicationController
  def create
    service = UserRegistrationService.new(
      params[:name],
      params[:email],
      params[:password],
      params[:company_name]
    )
    
    user = service.call
    render json: user
  rescue => e
    render json: { error: e.message }, status: :unprocessable_entity
  end
end
```

### ✅ Benefits:
- Clean separation of concerns
- Easy to test each step independently
- Reusable from multiple places
- Clear and readable flow
- Single responsibility principle

---

## 🏗️ Service Object Patterns & Conventions

### Pattern 1: Call Method (Most Common)

```ruby
class SendWelcomeEmailService
  def self.call(user)
    new(user).call
  end

  def initialize(user)
    @user = user
  end

  def call
    UserMailer.welcome(@user).deliver_later
  end
end
```

Usage:
```ruby
SendWelcomeEmailService.call(user)
```

---

### Pattern 2: Success/Failure Response

```ruby
class PaymentProcessingService
  def initialize(user, amount)
    @user = user
    @amount = amount
    @result = { success: false, message: nil, data: nil }
  end

  def call
    validate_user
    process_payment
    create_transaction_record
    send_confirmation_email

    @result
  end

  private

  def validate_user
    unless @user.payment_method_valid?
      @result[:message] = "Invalid payment method"
      return false
    end
  end

  def process_payment
    response = PaymentGateway.charge(@user, @amount)
    
    unless response.success?
      @result[:message] = "Payment failed: #{response.error}"
      return false
    end

    @result[:success] = true
    @result[:data] = response
  end

  def create_transaction_record
    Transaction.create(user_id: @user.id, amount: @amount)
  end

  def send_confirmation_email
    UserMailer.payment_confirmation(@user).deliver_later
  end
end
```

Usage:
```ruby
result = PaymentProcessingService.new(user, 100).call

if result[:success]
  puts "Payment processed: #{result[:data]}"
else
  puts "Error: #{result[:message]}"
end
```

---

### Pattern 3: Using Result/Response Objects

```ruby
class ServiceResult
  attr_reader :success, :data, :error

  def initialize(success: false, data: nil, error: nil)
    @success = success
    @data = data
    @error = error
  end

  def success?
    success
  end

  def failure?
    !success
  end
end

class CreateOrderService
  def initialize(user, items)
    @user = user
    @items = items
  end

  def call
    return failure("No items provided") if @items.empty?
    return failure("Insufficient inventory") unless check_inventory

    order = create_order
    update_inventory
    send_confirmation

    success(order)
  end

  private

  def check_inventory
    @items.all? { |item| item.quantity_available? }
  end

  def create_order
    Order.create(user: @user, items: @items)
  end

  def update_inventory
    @items.each { |item| item.decrease_quantity! }
  end

  def send_confirmation
    OrderMailer.confirmation(@user).deliver_later
  end

  def success(data)
    ServiceResult.new(success: true, data: data)
  end

  def failure(error)
    ServiceResult.new(success: false, error: error)
  end
end
```

Usage:
```ruby
result = CreateOrderService.new(user, items).call

if result.success?
  puts "Order created: #{result.data.id}"
else
  puts "Error: #{result.error}"
end
```

---

## 📁 Directory Structure

Service Objects typically live in:

```
app/
└── services/
    ├── user_registration_service.rb
    ├── payment_processing_service.rb
    ├── email_notification_service.rb
    └── analytics_service.rb
```

You may need to add this to `config/autoload_paths.rb`:

```ruby
config.autoload_paths += [Rails.root.join("app/services").to_s]
```

Or in newer Rails (>= 6), it's autoloaded by default.

---

## 🎯 When to Use Service Objects

### ✅ Use Service Objects for:

1. **Complex Business Logic**
   - Multi-step workflows
   - External API calls
   - Complex calculations

```ruby
class GenerateMonthlyReportService
  # 5+ steps involved
end
```

2. **Operations Involving Multiple Models**
   - Creating related records
   - Updating multiple tables
   - Cross-model transactions

```ruby
class TransferFundsService
  # Updates accounts, creates transactions, sends notifications
end
```

3. **Reusable Logic Across Multiple Controllers**
   - User registration (admin panel, API, website)
   - Payment processing (checkout, subscription renewal)

4. **External API Integration**
   - Calling third-party services
   - Webhook processing
   - Data synchronization

```ruby
class SyncWithSlackService
  # Calls Slack API
end
```

5. **Complex Validations or Calculations**

```ruby
class CalculateShippingCostService
  # Complex logic with multiple conditions
end
```

### ❌ Do NOT Use Service Objects for:

- Simple CRUD operations
- Single model validations
- Basic associations

```ruby
# ❌ Overkill - just use model method
class FindUserService
  def call(id)
    User.find(id)
  end
end

# ✅ Better - use scope or model method
class User < ApplicationRecord
  scope :active, -> { where(status: "active") }
end
```

---

## 🧪 Testing Service Objects

Service Objects are **much easier to test** than controllers or models.

### Example: Unit Test

```ruby
require 'rails_helper'

RSpec.describe UserRegistrationService do
  describe '#call' do
    let(:service) do
      UserRegistrationService.new(
        "John",
        "john@example.com",
        "password123",
        "Acme Corp"
      )
    end

    it "creates a user" do
      expect { service.call }.to change(User, :count).by(1)
    end

    it "creates a company" do
      expect { service.call }.to change(Company, :count).by(1)
    end

    it "sends welcome email" do
      user = service.call
      expect(UserMailer).to have_received(:welcome).with(user)
    end

    it "returns the created user" do
      user = service.call
      expect(user).to be_a(User)
      expect(user.email).to eq("john@example.com")
    end
  end
end
```

---

## 🔄 Service Objects vs Other Patterns

| Pattern | Use Case | Example |
|---------|----------|---------|
| **Service Object** | Multi-step complex workflows | User registration, payment processing |
| **Model Method** | Simple single-model logic | Validations, calculations |
| **Concern** | Shared code across multiple models | Statusable, Publishable |
| **Form Object** | Complex forms with multiple models | Signup form with nested attributes |
| **Query Object** | Complex database queries | Advanced filtering and searching |
| **Repository Pattern** | Abstract data access layer | Database abstraction |

---

## 💡 Real-World Examples

### Example 1: Social Media Post Creation

```ruby
class CreatePostService
  def initialize(user, content, attachments = [])
    @user = user
    @content = content
    @attachments = attachments
  end

  def call
    create_post
    process_attachments
    create_activity_log
    notify_followers
    update_cache

    @post
  end

  private

  attr_reader :user, :content, :attachments
  attr_accessor :post

  def create_post
    @post = user.posts.create!(content: content)
  end

  def process_attachments
    attachments.each do |attachment|
      @post.attachments.create!(file: attachment)
    end
  end

  def create_activity_log
    ActivityLog.create!(
      user_id: user.id,
      action: "created_post",
      resource_id: @post.id
    )
  end

  def notify_followers
    user.followers.each do |follower|
      Notification.create!(
        user_id: follower.id,
        message: "#{user.name} posted new content"
      )
    end
  end

  def update_cache
    Rails.cache.delete("user:#{user.id}:posts")
  end
end
```

---

### Example 2: Batch Import Users

```ruby
class ImportUsersService
  def initialize(csv_file)
    @csv_file = csv_file
    @results = { success: 0, failed: 0, errors: [] }
  end

  def call
    CSV.foreach(@csv_file, headers: true) do |row|
      import_user(row)
    end

    @results
  end

  private

  def import_user(row)
    user = User.new(
      name: row["name"],
      email: row["email"],
      department: row["department"]
    )

    if user.save
      @results[:success] += 1
    else
      @results[:failed] += 1
      @results[:errors] << {
        row: row,
        errors: user.errors.full_messages
      }
    end
  end
end
```

---

## ⚠️ Important Notes

### 1. Service Objects Are Not Magic

Service Objects are just **well-organized Ruby classes**.

```ruby
# Service Object is just this:
class MyService
  def initialize(params)
    @params = params
  end

  def call
    # do stuff
  end
end

# No special rails magic needed
```

### 2. Use Dependency Injection

Make service objects flexible by injecting dependencies:

```ruby
class SendEmailService
  def initialize(user, mailer: UserMailer)
    @user = user
    @mailer = mailer
  end

  def call
    @mailer.welcome(@user).deliver_later
  end
end

# Easy to test with mock mailer
SendEmailService.new(user, mailer: MockMailer).call
```

### 3. Avoid Callback Hell

Don't nest service objects too deeply:

```ruby
# ❌ Avoid
ServiceA.call { |result| ServiceB.call(result) { |result2| ... } }

# ✅ Better
result_a = ServiceA.call(params)
result_b = ServiceB.call(result_a)
result_c = ServiceC.call(result_b)
```

### 4. Keep Models Thin but Not Empty

Service objects help, but models should still have:
- Validations
- Associations
- Simple query scopes
- Domain-specific methods

---

## 🎓 Interview Tips

### Common Questions:

**Q: Why use Service Objects instead of fat models?**
- A: Service objects provide better organization, reusability, and testability. They follow single responsibility principle.

**Q: When would you NOT use a Service Object?**
- A: For simple CRUD, basic model methods, or single-step operations.

**Q: How do you test Service Objects?**
- A: Unit tests - they're just Ruby classes with no Rails dependencies.

**Q: Service Object vs Concern vs Query Object?**
- A: Service objects for workflows, concerns for shared code, query objects for complex queries.

