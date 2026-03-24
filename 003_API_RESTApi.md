# REST, API, and REST API — Complete Guide

---

## What is an API?

**API** stands for **Application Programming Interface**.  
Think of it as a **contract between two software applications** — it defines how they can talk to each other.

### Real-World Analogy
A waiter in a restaurant:
- **You (client)** don't go into the kitchen directly
- You tell the **waiter (API)** what you want
- The waiter brings it back from the **kitchen (server)**

### Rails Context
Every time you call `User.find(1)`, that's ActiveRecord's internal API.  
External APIs work the same way — just over a network.

---

## What is REST?

**REST** stands for **Representational State Transfer**.  
It's an **architectural style** (a set of rules/conventions) for designing networked applications, introduced by Roy Fielding in 2000.

### 6 Core REST Principles

| # | Principle | Meaning |
|---|-----------|---------|
| 1 | **Client-Server** | UI and data storage are separated |
| 2 | **Stateless** | Each request contains all info needed; server stores no session |
| 3 | **Cacheable** | Responses can be cached to improve performance |
| 4 | **Uniform Interface** | Consistent way to interact with resources |
| 5 | **Layered System** | Client doesn't know if it's talking to the real server or a proxy |
| 6 | **Code on Demand** *(optional)* | Server can send executable code to client |

> **Most important:** Stateless + Uniform Interface — every request stands alone and uses standard HTTP methods.

---

## What is a REST API?

A **REST API** is an API that **follows REST principles over HTTP**.  
It maps your data (resources) to URLs and uses HTTP verbs to perform actions.

### HTTP Methods (Verbs)

| Method | Action | Description |
|--------|--------|-------------|
| `GET` | Read | Retrieve a resource |
| `POST` | Create | Create a new resource |
| `PUT` | Update | Replace an entire resource |
| `PATCH` | Update | Partially update a resource |
| `DELETE` | Delete | Remove a resource |

### RESTful Routes (Rails Convention)

```
GET    /posts          → index   (list all posts)
POST   /posts          → create  (create a post)
GET    /posts/:id      → show    (get one post)
PUT    /posts/:id      → update  (replace a post)
PATCH  /posts/:id      → update  (partial update)
DELETE /posts/:id      → destroy (delete a post)
```

> In Rails, `resources :posts` in `routes.rb` generates all 7 RESTful routes automatically.

---

## A Real HTTP Interaction

**Request:**
```http
GET /posts/42
Accept: application/json
```

**Response:**
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 42,
  "title": "Hello World",
  "body": "My first post"
}
```

---

## Common HTTP Status Codes

| Code | Meaning | When Used |
|------|---------|-----------|
| `200` | OK | Successful GET / PATCH |
| `201` | Created | Successful POST |
| `204` | No Content | Successful DELETE |
| `400` | Bad Request | Invalid input from client |
| `401` | Unauthorized | Not authenticated |
| `403` | Forbidden | Authenticated but not allowed |
| `404` | Not Found | Resource doesn't exist |
| `422` | Unprocessable Entity | Validation errors |
| `500` | Internal Server Error | Something broke on the server |

---

## How They All Connect

```
You (Client)
    │
    │  HTTP Request (GET /posts/42)
    ▼
REST API  ←── Rules applied here (stateless, uniform interface)
    │
    │  JSON Response
    ▼
Your Rails App (Server)
```

| Term | What it is |
|------|-----------|
| **API** | The *interface* — the door between two systems |
| **REST** | The *rules* — how that door should behave |
| **REST API** | A door built following those rules, over HTTP |

---

## Quick Summary

- **API** = Interface for apps to communicate
- **REST** = A set of rules/conventions for designing that interface
- **REST API** = An API built following REST rules over HTTP
- **Rails** already follows REST by convention with `resources` routing