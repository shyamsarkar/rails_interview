# Caching Strategies in Ruby on Rails

**Caching** stores frequently accessed or expensive-to-generate data so Rails can return it faster without repeating database queries or computations.

## 1. Fragment Caching

Caches a specific portion of a view instead of the entire page.

```erb
<% cache @product do %>
  <h2><%= @product.name %></h2>
  <p><%= @product.price %></p>
<% end %>
```

**Use case:** Product details, navigation menus, reusable UI components.

## 2. Russian Doll Caching

Nested fragment caching. A parent fragment contains smaller cached fragments.

```erb
<% cache @product do %>
  <h2><%= @product.name %></h2>

  <% @product.reviews.each do |review| %>
    <% cache review do %>
      <%= review.content %>
    <% end %>
  <% end %>
<% end %>
```

**Use case:** Pages with nested resources. Individual fragments can be reused independently.

## 3. Low-Level Caching

Caches the result of expensive operations or database queries using `Rails.cache`.

```ruby
Rails.cache.fetch("popular_products", expires_in: 10.minutes) do
  Product.popular.to_a
end
```

**Use case:** Expensive calculations, API responses, frequently accessed data.

## 4. HTTP Caching

Uses HTTP headers such as `ETag` and `Last-Modified` to avoid sending unchanged responses.

```ruby
def show
  @product = Product.find(params[:id])

  fresh_when(@product)
end
```

**Use case:** APIs and web pages where clients frequently request unchanged resources.

## 5. SQL Query Caching

Rails automatically caches identical SQL queries within a single request.

```ruby
Product.where(active: true).to_a
Product.where(active: true).to_a
```

The second identical query can use the request-level query cache instead of hitting the database.

**Use case:** Avoiding duplicate database queries within one request.

## 6. Cache Stores

Rails supports different storage backends for cached data.

| Cache Store | Purpose |
|---|---|
| MemoryStore | Stores cache in application memory |
| RedisCacheStore | Stores cache in Redis |
| MemCacheStore | Uses Memcached |
| FileStore | Stores cache on disk |
| NullStore | Disables caching |

## Quick Interview Revision

| Strategy | What Does It Cache? |
|---|---|
| Fragment Caching | Part of a view |
| Russian Doll Caching | Nested view fragments |
| Low-Level Caching | Data or computation results |
| HTTP Caching | HTTP responses using validators |
| SQL Query Caching | Repeated SQL queries per request |

## Interview Tip

If asked which caching strategy you would use in a production Rails application, explain that it depends on the use case.

For shared, frequently accessed data across multiple application instances, **Redis is a common choice** using Rails' `RedisCacheStore`.