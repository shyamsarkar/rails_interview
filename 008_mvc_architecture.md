# MVC Architecture in Ruby on Rails

---

## 🔹 What is MVC?

MVC stands for:

- **M → Model**
- **V → View**
- **C → Controller**

It is a design pattern that separates application logic into three parts.

👉 Purpose: **Separation of concerns (अलग-अलग जिम्मेदारी)**

---

## 🔹 1. Model (Business Logic + Database)

### 📌 Definition
The **Model** handles:
- Database interaction
- Business logic
- Validations

👉 It represents data and rules of the application.

---

### 🧠 Example
```ruby
class User < ApplicationRecord
  validates :email, presence: true
end
```

---

### 🎯 Responsibilities
- CRUD operations
- Validations
- Associations (`has_many`, `belongs_to`)

---

## 🔹 2. View (UI Layer)

### 📌 Definition
The **View** is responsible for:
- Displaying data to the user
- UI (HTML, ERB templates)

👉 It does NOT contain business logic.

---

### 🧠 Example
```erb
<h1><%= @user.name %></h1>
<p><%= @user.email %></p>
```

---

### 🎯 Responsibilities
- Rendering UI
- Showing data from controller

---

## 🔹 3. Controller (Middle Layer)

### 📌 Definition
The **Controller** acts as a bridge between Model and View.

👉 It handles user requests and decides what to do.

---

### 🧠 Example
```ruby
class UsersController < ApplicationController
  def show
    @user = User.find(params[:id])
  end
end
```

---

### 🎯 Responsibilities
- Receive request
- Call Model
- Pass data to View

---

## 🔄 Request Flow (Very Important)

1. User sends request (browser)
2. Router maps URL → Controller action
3. Controller calls Model
4. Model fetches data from DB
5. Controller sends data to View
6. View renders response
7. Response sent back to user

---

### 🧠 Example Flow

👉 URL: `/users/1`

- Router → `UsersController#show`
- Controller → fetch user
- Model → query DB
- View → display user details

---

## 🔥 Diagram (Simple)
```
User → Controller → Model → Database
             ↓
           View
             ↓
           User
```

---

## 🎯 Key Benefits
- Separation of concerns
- Easy to maintain
- Scalable architecture
- Code reusability

---

## ⚡ Quick Summary

| Component | Role |
|---|---|
| Model | Data + Business Logic |
| View | UI / Presentation |
| Controller | Request handling + flow control |

---

## 🧠 Easy Way to Remember
- **Model** → Data
- **View** → UI
- **Controller** → Logic / Flow
