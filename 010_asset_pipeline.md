## 🔹 What is Asset Pipeline?

The **Asset Pipeline** is a Rails feature used to **manage, organize, and optimize assets** like:

- CSS
- JavaScript
- Images

👉 It helps in improving performance and maintainability.

---

## 🔹 Why Asset Pipeline is Needed?

- Combine multiple files → reduce HTTP requests
- Minify files → reduce size
- Organize assets properly
- Cache assets for faster loading

👉 Goal: **Better performance**

---

## 🔹 Key Features

### 1. Concatenation (Combine Files)
Multiple CSS/JS files are combined into one.

---

### 2. Minification
Removes:
- Spaces
- Comments
- Extra characters

👉 Makes files smaller.

---

### 3. Fingerprinting (Digest)

Adds a unique hash to file names:
```
application.css → application-abc123.css
```

👉 Helps in caching (cache busting).

---

### 4. Precompilation

Assets are compiled before deployment.
```bash
rails assets:precompile
```

---

## 🔹 Folder Structure
```
app/assets/
  ├── stylesheets/
  ├── javascripts/
  └── images/
```

---

## 🔹 Manifest File

Used to include assets:
```js
// app/assets/javascripts/application.js
//= require jquery
//= require_tree .
```
```css
/* app/assets/stylesheets/application.css */
/*= require_self */
/*= require_tree . */
```

---

## 🔹 Example

Instead of loading:
```
style1.css
style2.css
script1.js
script2.js
```

👉 Asset Pipeline converts to:
```
application.css
application.js
```

---

## 🎯 Key Points
- Improves performance
- Handles asset organization
- Uses fingerprinting for caching
- Works with precompilation