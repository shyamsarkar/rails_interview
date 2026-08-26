# Scope vs Namespace in Rails Routes

## Q. What is the difference between these Rails routing methods?

Assume every example is inside `config/routes.rb` and contains:

```ruby
resources :users
```

The examples below show the representative collection route `GET /.../users`, which maps to the `index` action. The same scope rules apply to the other RESTful routes.

## Comparison

| Route declaration | URL prefix | Controller module | Controller file | Controller class | Representative helper |
| --- | --- | --- | --- | --- | --- |
| `namespace :v1` | `/v1` | `V1::` | `app/controllers/v1/users_controller.rb` | `V1::UsersController` | `v1_users_path` |
| `scope :v1` | `/v1` | None | `app/controllers/users_controller.rb` | `UsersController` | `users_path` |
| `scope path: "v1"` | `/v1` | None | `app/controllers/users_controller.rb` | `UsersController` | `users_path` |
| `scope path: "v1", module: "v1"` | `/v1` | `V1::` | `app/controllers/v1/users_controller.rb` | `V1::UsersController` | `users_path` |
| `scope module: "v1"` | None | `V1::` | `app/controllers/v1/users_controller.rb` | `V1::UsersController` | `users_path` |

For example, the first row is:

```ruby
namespace :v1 do
  resources :users
end
```

It creates `GET /v1/users`, dispatches to `V1::UsersController#index`, and provides `v1_users_path`. For a member route, the equivalent helper is `v1_user_path(user)`.

## 1. `namespace :v1`

```ruby
namespace :v1 do
  resources :users
end
```

`namespace` combines three behaviors:

- Adds `v1` to the URL.
- Looks for controllers in the `V1` module.
- Prefixes named route helpers with `v1_`.

Use it when the URL, controller module, and helper namespace should all match. It is the usual choice for a versioned API when routes should be clearly separated as `V1` routes.

## 2. `scope :v1`

```ruby
scope :v1 do
  resources :users
end
```

The positional argument is a path scope. It adds `/v1` to the URL, but it does not change the controller module or helper names. The route therefore uses `UsersController` and `users_path`.

Use it when only the public URL should contain `v1`, while controllers and helpers remain unnamespaced. The symbol and string forms are equivalent for this path-only use.

## 3. `scope path: "v1"`

```ruby
scope path: "v1" do
  resources :users
end
```

This is the explicit option form of the path-only scope. It has the same result as `scope :v1`: `/v1/users` maps to `UsersController`, with helpers such as `users_path`.

Use it when you want the path behavior to be obvious, or when combining it with other scope options later.

## 4. `scope path: "v1", module: "v1"`

```ruby
scope path: "v1", module: "v1" do
  resources :users
end
```

This adds both the URL prefix and controller module, but does not add a helper prefix. `/v1/users` maps to `V1::UsersController`, and the helper remains `users_path`.

Use it when the URL and controller organization should match, but application code should use unprefixed helpers. Add `as: "v1"` as a separate option if a helper prefix is also required.

## 5. `scope module: "v1"`

```ruby
scope module: "v1" do
  resources :users
end
```

This changes only controller lookup. `/users` maps to `V1::UsersController`, and the helper remains `users_path`.

Use it when controllers are organized in a module for code structure, while the public URL and helper names should stay unchanged.

## One-line summary

- `namespace :v1` = path + controller module + helper prefix.
- `scope :v1` = path only.
- `scope path: "v1"` = path only, written explicitly.
- `scope path: "v1", module: "v1"` = path + controller module.
- `scope module: "v1"` = controller module only.