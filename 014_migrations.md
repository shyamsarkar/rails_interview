# What is a Migration in Ruby on Rails?

A **Migration** is a way to manage and version-control your database schema using Ruby code instead of raw SQL.

👉 **"Git for your database structure"**

---

## Simple Definition
👉 Migration = Ruby code to create/modify database tables in a controlled way
```ruby
class CreateUsers < ActiveRecord::Migration[7.0]
  def change
    create_table :users do |t|
      t.string :name
      t.string :email
      t.timestamps
    end
  end
end
```

---

## How Migrations Work in Rails

**1. Migration Files** → stored in `db/migrate/`
- Format: `20260325120000_create_users.rb`
- Timestamp ensures execution order

**2. Run Migrations**
```bash
rails db:migrate
```
Checks pending → runs in order → updates tracking table

**3. Schema Tracking** → `schema_migrations` table
- Stores executed migration versions (timestamps)
- Prevents re-running same migration

**4. Rollback**
```bash
rails db:rollback   # reverts last migration
```

**5. Migration Status**
```bash
rails db:migrate:status   # shows up (run) / down (pending)
```

---

## Types of Migration Changes
```ruby
create_table :users do |t|   # Create table
  t.string :name
end

add_column :users, :age, :integer   # Add column
remove_column :users, :age          # Remove column
add_index :users, :email            # Add index
```

---

## `change` vs `up`/`down`
```ruby
# change (most used) — Rails auto-handles rollback
def change
  add_column :users, :age, :integer
end

# up/down — manual control
def up   = add_column :users, :age, :integer
def down = remove_column :users, :age
```

---

## Key Concepts
- **Idempotency** → migration runs only once
- **Version Control** → migrations tracked like commits
- **`db/schema.rb`** → current DB structure snapshot

---

## Real-world Flow
1. Developer writes migration
2. Runs `rails db:migrate`
3. DB schema updates
4. Other devs pull code → run migrations → same DB structure

---

👉 **One Line:** Migrations = version-controlled, Ruby-based database schema management