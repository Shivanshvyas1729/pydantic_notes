# Flask Request Data Cheat Sheet

Flask decides where to put incoming data based on the **`Content-Type`** header sent by the client.

| Content-Type                        | Flask Object                     | Used For                          |
| ----------------------------------- | -------------------------------- | --------------------------------- |
| `application/json`                  | `request.json`                   | JSON data (APIs)                  |
| `multipart/form-data`               | `request.form` + `request.files` | Forms with file uploads           |
| `application/x-www-form-urlencoded` | `request.form`                   | Regular HTML forms                |
| URL Query Parameters                | `request.args`                   | Data in the URL (e.g., `?page=2`) |

---

## 1. `request.json`

Used when the client sends JSON.

### Client Request

```http
POST /query
Content-Type: application/json
```

```json
{
    "question": "What is AI?",
    "user": "Alice"
}
```

### Flask

```python
data = request.json

question = data["question"]
user = data["user"]
```

---

## 2. `request.form`

Used for **text fields** submitted from an HTML form.

### HTML

```html
<form method="POST">
    <input type="text" name="username">
    <input type="password" name="password">
    <button type="submit">Login</button>
</form>
```

### Flask

```python
username = request.form["username"]
password = request.form["password"]
```

---

## 3. `request.files`

Used for **uploaded files**.

### HTML

```html
<form method="POST" enctype="multipart/form-data">
    <input type="file" name="resume">
    <button type="submit">Upload</button>
</form>
```

### Flask

```python
resume = request.files["resume"]
```

`resume` is a `FileStorage` object that you can save or process.

---

## 4. Text + File Together

When using `multipart/form-data`, Flask automatically separates text fields and files.

### HTML

```html
<form method="POST" enctype="multipart/form-data">

    <input type="text" name="username">

    <input type="text" name="department">

    <input type="file" name="resume">

    <button type="submit">
        Upload
    </button>

</form>
```

### Flask

```python
username = request.form["username"]
department = request.form["department"]

resume = request.files["resume"]
```

Notice that:

* Text inputs go into **`request.form`**
* File inputs go into **`request.files`**

---

# How Does Flask Know?

It **doesn't inspect the data itself**.

Instead, it checks the HTTP **`Content-Type`** header sent by the client.

```
HTTP Request
      │
      ▼
Content-Type
      │
 ┌──────────────┬───────────────────────┬─────────────────────────────┐
 │              │                       │
application/    multipart/              application/
json            form-data               x-www-form-urlencoded
 │              │                       │
 ▼              ▼                       ▼
request.json    request.form            request.form
                request.files
```

---

# Can the Same Key Exist?

## 1. Inside JSON ❌

Duplicate keys are **not valid**.

```json
{
    "name": "Alice",
    "name": "Bob"
}
```

Python keeps only the last value:

```python
{"name": "Bob"}
```

---

## 2. Between `request.form` and `request.files` ✅

Yes, because they are different collections.

HTML:

```html
<input type="text" name="file">
<input type="file" name="file">
```

Flask:

```python
request.form["file"]      # Text value

request.files["file"]     # Uploaded file
```

Although this works, using the same name is confusing and generally not recommended.

---

## 3. Multiple Form Fields with the Same Name ✅

Example:

```html
<input type="checkbox" name="skills" value="Python">
<input type="checkbox" name="skills" value="Java">
<input type="checkbox" name="skills" value="C++">
```

If the user selects **Python** and **C++**:

```python
request.form.getlist("skills")
```

Output:

```python
["Python", "C++"]
```

Using:

```python
request.form["skills"]
```

returns only one value.

---

# Rule to Remember

* **`request.json`** → JSON data sent by APIs.
* **`request.form`** → Text fields from HTML forms.
* **`request.files`** → Uploaded files.
* Flask chooses which one to use based on the **`Content-Type`** header.
* Duplicate keys are **not allowed inside a JSON object**.
* The same field name **can exist in `request.form` and `request.files`**, because they are separate collections.
* If multiple form fields share the same name (e.g., checkboxes), use **`request.form.getlist()`** to retrieve all values.
