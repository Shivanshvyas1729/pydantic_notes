
<details><summary>basics</summary>

# Flask Basics (Complete Beginner Guide)

## What is Flask?

Flask is a **lightweight Python web framework** used to build:

* Websites
* REST APIs
* Machine Learning backends
* Chatbots
* File upload services
* Authentication systems

Unlike Django, Flask gives you only the essentials and lets you choose additional libraries when needed.

Install Flask:

```bash
pip install flask
```

---

# 1. Creating Your First Flask App

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello, Flask!"

if __name__ == "__main__":
    app.run(debug=True)
```

### Explanation

```python
app = Flask(__name__)
```

Creates the Flask application.

`__name__` tells Flask where the application is located so it can find templates and static files.

---

```python
@app.route("/")
```

Maps a URL to a Python function.

When someone visits

```
http://localhost:5000/
```

Flask executes:

```python
home()
```

---

```python
def home():
    return "Hello Flask!"
```

This function is called a **View Function**.

Whatever it returns becomes the HTTP response.

---

```python
app.run(debug=True)
```

Starts the development server.

`debug=True`

* Automatically reloads on code changes
* Shows detailed error messages

---

# 2. Routes

Routes define which function runs for a URL.

```python
@app.route("/")
def home():
    return "Home"

@app.route("/about")
def about():
    return "About"

@app.route("/contact")
def contact():
    return "Contact"
```

Visiting

```
/
```

calls

```python
home()
```

Visiting

```
/about
```

calls

```python
about()
```

---

# 3. Dynamic Routes

Routes can accept variables.

```python
@app.route("/user/<name>")
def user(name):
    return f"Hello {name}"
```

Visiting

```
/user/Alice
```

returns

```
Hello Alice
```

Multiple parameters:

```python
@app.route("/add/<int:a>/<int:b>")
def add(a, b):
    return str(a + b)
```

URL

```
/add/10/20
```

Output

```
30
```

Flask converts the values automatically.

Supported converters:

```
<int:id>

<float:price>

<string:name>

<path:file_path>

<uuid:id>
```

---

# 4. HTTP Methods

HTTP methods tell the server what action the client wants.

| Method | Purpose        |
| ------ | -------------- |
| GET    | Read data      |
| POST   | Send new data  |
| PUT    | Update data    |
| PATCH  | Partial update |
| DELETE | Remove data    |

Example:

```python
@app.route("/login", methods=["GET", "POST"])
def login():
    if request.method == "POST":
        return "Login submitted"

    return "Login page"
```

---

# 5. Request Object

The client sends information.

Flask stores it inside `request`.

```python
from flask import request
```

Common attributes:

```
request.args

request.form

request.files

request.json

request.method

request.headers
```

---

# 6. URL Parameters

```
GET /search?name=Alice&age=25
```

Flask

```python
name = request.args.get("name")
age = request.args.get("age")
```

Result

```
Alice

25
```

---

# 7. Form Data

HTML

```html
<form method="POST">

    <input name="username">

    <input name="password">

    <button>Login</button>

</form>
```

Flask

```python
username = request.form["username"]

password = request.form["password"]
```

---

# 8. JSON Data

Client sends

```json
{
    "name":"Alice",
    "age":25
}
```

Flask

```python
data = request.json

name = data["name"]

age = data["age"]
```

---

# 9. File Upload

HTML

```html
<form method="POST"
      enctype="multipart/form-data">

    <input type="file" name="file">

    <button>Upload</button>

</form>
```

Flask

```python
file = request.files["file"]

file.save(file.filename)
```

---

# 10. Returning Responses

Simple text

```python
return "Hello"
```

HTML

```python
return "<h1>Hello</h1>"
```

JSON

```python
from flask import jsonify

return jsonify({
    "name":"Alice",
    "age":25
})
```

Status Code

```python
return jsonify({
    "error":"Not Found"
}),404
```

---

# 11. Templates

Folder structure

```
project/

    app.py

    templates/

        index.html
```

HTML

```html
<h1>Hello {{name}}</h1>
```

Flask

```python
from flask import render_template

@app.route("/")
def home():
    return render_template(
        "index.html",
        name="Alice"
    )
```

Output

```
Hello Alice
```

---

# 12. Static Files

Folder

```
project/

    static/

        style.css

        logo.png
```

HTML

```html
<link rel="stylesheet"
href="{{ url_for('static',
filename='style.css') }}">
```

---

# 13. Redirect

```python
from flask import redirect
```

```python
@app.route("/google")
def google():
    return redirect("https://google.com")
```

---

# 14. url_for()

Instead of hardcoding URLs

Bad

```python
return redirect("/login")
```

Good

```python
return redirect(url_for("login"))
```

If route changes later, your code still works.

---

# 15. Error Handling

```python
@app.errorhandler(404)
def not_found(error):

    return "Page not found",404
```

---

# 16. Sessions

Store user information.

```python
from flask import session

app.secret_key="secret"

session["user"]="Alice"

print(session["user"])
```

---

# 17. Blueprint

Used to split large projects.

Example

```
project/

app.py

users/

    routes.py

products/

    routes.py
```

Each module has its own routes.

---

# 18. Project Structure

Small Project

```
project/

app.py

templates/

static/
```

Medium Project

```
project/

app.py

routes/

models/

services/

utils/

templates/

static/

config.py
```

Large Project

```
project/

app.py

routes/

controllers/

models/

services/

repository/

middleware/

utils/

config.py

templates/

static/
```

---

# 19. Typical Request Flow

```
Browser / Postman
        │
        ▼
HTTP Request
        │
        ▼
Flask Route
        │
        ▼
Read request data
(request.args /
 request.form /
 request.json /
 request.files)
        │
        ▼
Business Logic
(Database / AI / Service)
        │
        ▼
Prepare Response
        │
        ▼
return jsonify(...)
        │
        ▼
Client receives response
```

---

# 20. Flask Objects You'll Use Most

| Object          | Purpose                    |
| --------------- | -------------------------- |
| Flask           | Creates the application    |
| app             | Main Flask application     |
| @app.route      | Maps URLs to functions     |
| request         | Reads incoming client data |
| jsonify         | Returns JSON responses     |
| render_template | Renders HTML templates     |
| redirect        | Redirects to another URL   |
| url_for         | Generates URLs dynamically |
| session         | Stores user session data   |

---

# Flask Workflow Summary

1. Client sends an HTTP request.
2. Flask matches the request URL to a route.
3. The route function executes.
4. Read input using:

   * `request.args` (URL parameters)
   * `request.form` (HTML form fields)
   * `request.files` (uploaded files)
   * `request.json` (JSON body)
5. Process the data (database, AI model, file handling, etc.).
6. Return a response using:

   * Plain text
   * HTML (`render_template`)
   * JSON (`jsonify`)
   * Redirect (`redirect`)
7. Flask sends the HTTP response back to the client.
</details>













<details><summary> Flask Request Data Cheat Sheet </summary>







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
* </details>
