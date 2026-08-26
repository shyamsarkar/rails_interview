# Rails Initializers

## Overview

Initializers are Ruby files that run once during application boot time, before any requests are served. They're used to configure your application, load dependencies, and set up third-party gems. Initializers live in `config/initializers/` and are executed in **alphabetical order** as part of the Rails startup sequence.

## Key Characteristics

- **Execution Timing**: Run after Rails framework initialization but before application starts serving requests
- **Environment**: Executed in all environments (development, test, production) unless filtered
- **Order**: Loaded alphabetically by filename
- **Scope**: Available in the global context for the entire application lifetime
- **Frequency**: Runs only once per server start

## File Location

```
config/
  initializers/
    001_database_setup.rb
    002_redis_config.rb
    003_constants.rb
    devise.rb
    paperclip.rb
    rack_timeout.rb
```

**Naming Convention**: Use numeric prefixes (001_, 002_) to control execution order when dependencies matter.

## Common Use Cases

### 1. **Gem Configuration**
```ruby
# config/initializers/devise.rb
Devise.setup do |config|
  config.mailer_sender = 'noreply@example.com'
  config.password_length = 6..128
end
```

### 2. **Constants Definition**
```ruby
# config/initializers/constants.rb
API_KEY = ENV['API_KEY']
ALLOWED_HOSTS = ['example.com', 'www.example.com']
```

### 3. **Monkey Patching (Careful!)**
```ruby
# config/initializers/core_extensions.rb
class String
  def reverse_words
    split(' ').reverse.join(' ')
  end
end
```

### 4. **Custom Configuration**
```ruby
# config/initializers/app_config.rb
Rails.application.config.max_upload_size = 10.megabytes
Rails.application.config.session_timeout = 30.minutes
```

### 5. **External Service Setup**
```ruby
# config/initializers/aws.rb
Aws.config.update(
  region: ENV['AWS_REGION'],
  credentials: Aws::Credentials.new(ENV['AWS_ACCESS_KEY_ID'], ENV['AWS_SECRET_ACCESS_KEY'])
)
```

### 6. **ActiveSupport Callbacks**
```ruby
# config/initializers/callbacks.rb
Rails.application.config.after_initialize do
  puts "Application initialized!"
end
```

## Execution Order and Dependencies

When dependencies exist between initializers, control execution order:

```ruby
# config/initializers/001_logging_setup.rb
# Must run first
Rails.logger.level = Logger::DEBUG

# config/initializers/002_cache_setup.rb
# Depends on logging_setup
Rails::Cache.set_default_cache_store
```

## Environment-Specific Initializers

### Option 1: Check Environment Inside Initializer
```ruby
# config/initializers/scheduler.rb
if Rails.env.production?
  Scheduler.start
end
```

### Option 2: Use Environment-Specific Files
```
config/
  initializers/
    scheduler.rb  # Shared logic
  initializers/
    development/
      debug.rb
    production/
      monitoring.rb
```

Then manually require them in the base initializer.

## Lifecycle in Rails Boot Sequence

1. **Rails Framework Initializes** (log levels, load paths, gems)
2. **Railties Initialize** (engines, extensions)
3. **app/models, app/controllers, app/helpers Load**
4. **Initializers Run** (in alphabetical order) ← **HERE**
5. **Finalization** (routes load, caches warm up)
6. **Ready to Accept Requests**

## Best Practices

### ✅ DO

- **Keep it Simple**: Each initializer should have a single responsibility
- **Use Meaningful Names**: Prefix with numbers if order matters
- **Check Environment**: Conditionally load based on environment
- **Handle Errors**: Use begin/rescue to prevent boot failures
- **Document Dependencies**: Comment on execution order requirements
- **Use Comments**: Explain why the initializer exists

```ruby
# config/initializers/001_error_tracking.rb
# Must run first to catch boot-time errors
begin
  Sentry.init do |config|
    config.dsn = ENV['SENTRY_DSN']
    config.traces_sample_rate = 0.1
  end
rescue => e
  Rails.logger.error("Sentry initialization failed: #{e.message}")
end
```

### ❌ DON'T

- **Don't Call Rails.application.config.to_prepare**: This is for class reloading in development
- **Don't Assume Load Order**: Unless explicitly numbered
- **Don't Load Heavy Dependencies**: Delay until first use (lazy loading)
- **Don't Create Database Records**: Initializers run before migrations
- **Don't Reference Other Models Directly**: Can cause circular dependencies

```ruby
# ❌ WRONG
# config/initializers/bad.rb
User.create(email: 'admin@example.com')  # Fails if table doesn't exist

# ✅ RIGHT
# Use a Rake task or database seed instead
```

## Debugging Initializers

### View Initialization Order
```bash
$ rails runner "puts Rails.application.config.paths['config/initializers'].existent"
```

### Log During Boot
```ruby
# config/initializers/debug.rb
Rails.logger.info("Initializer running at #{Time.now}")
```

### Catch Boot Errors
```bash
$ rails server  # Shows errors during initialization
```

## Common Pitfalls

| Issue | Solution |
|-------|----------|
| Initializer doesn't run | Check alphabetical order, file naming, Rails.env condition |
| Circular dependencies | Split into separate initializers with numeric prefixes |
| Changes not reflected | Restart Rails server (initializers only run on boot) |
| Database errors | Use lazy initialization or Rake tasks instead |
| Too many in config/initializers/ | Consolidate related configs, use engines/gems |

## Initialization Hooks

Rails provides hooks into the initialization lifecycle:

```ruby
# config/initializers/hooks.rb

# After all initializers have loaded
Rails.application.config.after_initialize do
  puts "App fully initialized"
end

# Before Rails shuts down
at_exit do
  puts "Cleaning up..."
end
```

## Comparison: Initializers vs Other Setup Methods

| Method | When to Use | Timing |
|--------|-----------|--------|
| **Initializers** | App-wide configuration | Boot time |
| **Application.rb** | Core Rails config | Framework initialization |
| **Railtie** | Plugin/engine setup | Boot time (advanced) |
| **Rake Tasks** | One-time setup | Manual execution |
| **Database Seeds** | Initial data | Manual execution |
| **Migration** | Database schema | Migration execution |

## Real-World Example

```ruby
# config/initializers/001_core_config.rb
# Core application configuration
Rails.application.config.time_zone = 'UTC'
Rails.application.config.active_record.default_timezone = :utc

# config/initializers/002_external_services.rb
# Third-party service setup (depends on core config)
Stripe.api_key = ENV['STRIPE_API_KEY']
Twilio.setup(ENV['TWILIO_ACCOUNT_SID'], ENV['TWILIO_AUTH_TOKEN'])

# config/initializers/devise.rb
# Devise gem configuration
Devise.setup do |config|
  config.secret_key = Rails.application.secrets.devise_secret_key
  config.mailer_sender = ENV['MAILER_FROM']
end

# config/initializers/production_only.rb
# Production-specific setup
if Rails.env.production?
  Honeybadger.configure do |config|
    config.api_key = ENV['HONEYBADGER_API_KEY']
  end
end
```