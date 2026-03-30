# REST Convention
 
REST = **Re**presentational **S**tate **T**ransfer — a set of conventions for designing APIs using HTTP.
 
---
 
## Core Idea
 
Every **resource** (user, post, order) gets its own URL, and you use **HTTP verbs** to act on it.
 
---
 
## The 7 Standard REST Actions
 
Using `posts` as example:
 
| Action | HTTP Verb | URL | What it does |
|---|---|---|---|
| `index` | GET | `/posts` | list all posts |
| `show` | GET | `/posts/:id` | show one post |
| `new` | GET | `/posts/new` | show new form |
| `create` | POST | `/posts` | create a post |
| `edit` | GET | `/posts/:id/edit` | show edit form |
| `update` | PUT/PATCH | `/posts/:id` | update a post |
| `destroy` | DELETE | `/posts/:id` | delete a post |
 
> `new` and `edit` are **Rails-specific** (for rendering forms) — pure APIs don't need them.
 
---
 
## Rails Routes Example
 
```ruby
# config/routes.rb
resources :posts  # generates all 7 routes automatically
```
 
```bash
# verify with
rails routes
```
 
---
 
## Nested Resources
 
When a resource belongs to another:
 
```ruby
resources :posts do
  resources :comments  # /posts/:post_id/comments
end
```
 
| Action | URL |
|---|---|
| index | GET `/posts/:post_id/comments` |
| show | GET `/posts/:post_id/comments/:id` |
| create | POST `/posts/:post_id/comments` |
 
---
 
## Key Conventions
 
### URLs are nouns, not verbs
 
```
✅ GET  /posts
❌ GET  /getPosts
 
✅ DELETE /posts/1
❌ POST   /posts/deletePost
```
 
### HTTP verb defines the action
 
```
GET    → read, never modify data
POST   → create
PATCH  → partial update
PUT    → full replace
DELETE → destroy
```
 
### Responses use proper HTTP status codes
 
```
200 → OK
201 → Created
204 → No Content (successful delete)
400 → Bad Request
401 → Unauthorized
403 → Forbidden
404 → Not Found
422 → Unprocessable Entity (validation failed)
500 → Server Error
```
 
