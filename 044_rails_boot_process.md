# Rails Boot Process

Q. Explain how Rails boots up (rails s / rails c)

## Overview
Rails booting is the process of loading the framework, the application, and preparing it to handle requests.

---

## Step-by-step Boot Flow

### 1️⃣ Ruby & Bundler Initialization

- Ruby interpreter starts
- `config/boot.rb` is executed
- Bundler loads to manage dependencies
- `Gemfile.lock` is read to ensure exact gem versions
- All gems listed in the `Gemfile` are required

### 2️⃣ Load Rails Framework

Rails loads its core components:

**Core Components**
- **ActiveSupport** – Core extensions and utilities
- **ActiveModel** – Model interfaces, validations, callbacks
- **ActiveRecord** – ORM and database interaction

**ActionPack**
- **ActionController** – Controller layer
- **ActionDispatch** – Routing and middleware
- **ActionView** – Template rendering

**Other Components**
- **ActiveJob**
- **ActionMailer**
- **ActionCable**
- **ActiveStorage** (if enabled)

### 3️⃣ Application Bootstrapping

- `config/application.rb` is loaded
- Rails environment is selected (`development`, `test`, `production`)
- Environment-specific config from `config/environments/*.rb` is applied

### 4️⃣ Autoloading with Zeitwerk

Rails sets up Zeitwerk, the code loader:

- `app/models`
- `app/controllers`
- `app/helpers`
- `app/jobs`
- `lib/` (if configured)

**Note:** Constants are lazy-loaded on first access and reloaded automatically in development.

### 5️⃣ Run Initializers

- All files in `config/initializers/` are executed
- Used to configure gems, middleware, and application-wide behavior

### 6️⃣ Routing Setup

- `config/routes.rb` is loaded
- Maps HTTP verbs and URLs to controller actions

### 7️⃣ Rack Application Setup

- `config.ru` is evaluated
- Rails application is wrapped as a Rack application
- Middleware stack is finalized

### 8️⃣ Web Server Starts

- **Puma** starts
- Server binds to a port (default: `3000`)
