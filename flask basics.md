# 🌶️ Complete Flask Master Note & Fast-Learning Guide

---

## 📋 Table of Contents

- [Phase 1: Python Prerequisites](#phase-1-python-prerequisites)
  - [1.1 Decorators](#11-decorators)
  - [1.2 JSON Handling & Serialization](#12-json-handling--serialization)
  - [1.3 Exception Handling](#13-exception-handling)
- [Phase 2: Flask Fundamentals](#phase-2-flask-fundamentals)
  - [2.1 WSGI & Flask Core Architecture](#21-wsgi--flask-core-architecture)
  - [2.2 App Setup & Development Server](#22-app-setup--development-server)
  - [2.3 Routing & URL Parameters](#23-routing--url-parameters)
- [Phase 3: Request & Response Handling](#phase-3-request--response-handling)
  - [3.1 Request Object (Query Params, Body, Headers)](#31-request-object-query-params-body-headers)
  - [3.2 Response Handling & jsonify](#32-response-handling--jsonify)
  - [3.3 Cookies & Sessions](#33-cookies--sessions)
- [Phase 4: Templates & Static Files (Jinja2)](#phase-4-templates--static-files-jinja2)
  - [4.1 Jinja2 Templating & Inheritance](#41-jinja2-templating--inheritance)
- [Phase 5: Databases & SQLAlchemy ORM](#phase-5-databases--sqlalchemy-orm)
  - [5.1 Flask-SQLAlchemy Models & CRUD](#51-flask-sqlalchemy-models--crud)
  - [5.2 Model Relationships (1:N & N:M)](#52-model-relationships-1n--nm)
  - [5.3 Database Migrations (Flask-Migrate / Alembic)](#53-database-migrations-flask-migrate--alembic)
- [Phase 6: Modular Applications (Flask Blueprints)](#phase-6-modular-applications-flask-blueprints)
  - [6.1 Flask Blueprints](#61-flask-blueprints)
- [Phase 7: Authentication & Authorization](#phase-7-authentication--authorization)
  - [7.1 Password Hashing (Werkzeug)](#71-password-hashing-werkzeug)
  - [7.2 JWT Authentication (Flask-JWT-Extended)](#72-jwt-authentication-flask-jwt-extended)
- [Phase 8: REST API Development](#phase-8-rest-api-development)
  - [8.1 Schema Validation & Serialization (Marshmallow)](#81-schema-validation--serialization-marshmallow)
  - [8.2 Error Handling & Custom HTTP Exception Responses](#82-error-handling--custom-http-exception-responses)
- [Phase 9: Advanced Flask Architecture](#phase-9-advanced-flask-architecture)
  - [9.1 Application Factory Pattern](#91-application-factory-pattern)
  - [9.2 Middleware & Hooks (@app.before_request)](#92-middleware--hooks-appbefore_request)
  - [9.3 Background Tasks (Celery Integration)](#93-background-tasks-celery-integration)
  - [9.4 CORS & Rate Limiting](#94-cors--rate-limiting)
- [⚡ Quick Reference Cheat Sheets](#-quick-reference-cheat-sheets)
  - [Flask Beginner Basics & Architecture Flow](#flask-beginner-basics--architecture-flow)
  - [Flask Request Data & Content-Type Cheat Sheet](#flask-request-data--content-type-cheat-sheet)
  - [HTTP Status Codes Master Reference (Flask & FastAPI)](#http-status-codes-master-reference-flask--fastapi)

---

## Phase 1: Python Prerequisites

### 1.1 Decorators

#### 1. Definition
A Python decorator is a function that takes another function as an argument, extends or modifies its behavior without modifying its source code, and returns a new callable function.

#### 2. Why It Exists
Decorators allow developers to write clean, reusable, and DRY (Don't Repeat Yourself) code for cross-cutting concerns like route mapping, authentication checks, logging, performance profiling, and transaction management.

#### 3. Syntax
```python
from functools import wraps

def my_decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        # Code before target function call
        result = func(*args, **kwargs)
        # Code after target function call
        return result
    return wrapper

@my_decorator
def target_function():
    pass
```

#### 4. Parameters
- `func`: The original target function being wrapped.
- `*args`, `**kwargs`: Variable arguments passed dynamically to the target function.

#### 5. Examples
```python
from functools import wraps
import time

def timer_decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        res = func(*args, **kwargs)
        print(f"Execution time of {func.__name__}: {time.time() - start:.4f}s")
        return res
    return wrapper

@timer_decorator
def compute_data():
    return sum(range(1000000))
```

#### 6. Common Variations
- **Decorators with arguments**: Wrapping an additional outer function layer to accept configuration options (e.g. `@app.route('/path')` or `@roles_required('admin')`).
- **Class-based decorators**: Implementing `__call__` on a class to maintain decorator state across executions.

#### 7. Rules
- Always use `@functools.wraps(func)` on the inner wrapper function to preserve original function attributes (`__name__`, `__doc__`).
- Ensure `*args` and `**kwargs` are accepted and forwarded to avoid breaking signatures.

#### 8. Common Mistakes
- Forgetting to return the inner `wrapper` function from the outer decorator function.
- Omitting `@wraps(func)`, causing multiple decorated endpoint functions in Flask to overwrite each other's view function names.

#### 9. Best Practices
- Keep decorator logic focused purely on single concerns (e.g. auth vs logging).
- Test decorated view functions both wrapped and unwrapped.

#### 10. Related Topics
Functions, Closure, `@wraps`, First-class functions.

---

### 1.2 JSON Handling & Serialization

#### 1. Definition
The process of converting Python data structures (dicts, lists) into JSON formatted text strings (serialization/dumps) and converting JSON strings back into Python data structures (deserialization/loads).

#### 2. Why It Exists
HTTP APIs transfer text data across network sockets. JSON is the universal, language-agnostic standard data interchange format for modern REST APIs and front-end web clients.

#### 3. Syntax
```python
import json

# Python Dict -> JSON String
json_string = json.dumps(data_dict)

# JSON String -> Python Dict
data_dict = json.loads(json_string)
```

#### 4. Parameters
- `obj`: The target Python data object to serialize.
- `fp`: File pointer object (for `json.dump` / `json.load`).
- `indent`: Integer specifying formatting indent spaces.
- `default`: Function handler for non-serializable objects (e.g., datetime, UUID).

#### 5. Examples
```python
import json
from datetime import datetime

user = {"name": "Alice", "role": "admin", "joined": datetime.now().isoformat()}

# Serialize to string
json_data = json.dumps(user, indent=2)

# Deserialize back to dict
parsed_user = json.loads(json_data)
```

#### 6. Common Variations
- `json.dump(obj, f)` / `json.load(f)`: Reads and writes directly to open file objects.
- `Custom JSONEncoder`: Subclassing `json.JSONEncoder` to handle custom objects like Datetime or SQLAlchemy models.

#### 7. Rules
- Standard JSON only supports strings, numbers, booleans, arrays, objects, and null values.
- Keys in JSON objects must always be strings.

#### 8. Common Mistakes
- Attempting to serialize non-supported Python types (e.g., `datetime`, `set`, `SQLAlchemy Model`) directly, raising a `TypeError: Object of type X is not JSON serializable`.

#### 9. Best Practices
- Use custom encoders or library tools (like Marshmallow or Pydantic) to serialize complex class instances cleanly.

#### 10. Related Topics
Dicts, Parsing, `jsonify`, REST APIs, Marshmallow.

---

### 1.3 Exception Handling

#### 1. Definition
A structured language mechanism using `try`, `except`, `else`, and `finally` blocks to catch, manage, and recover from runtime errors without crashing the application process.

#### 2. Why It Exists
In server applications, unhandled errors cause server crashes or `500 Internal Server Error` responses. Exception handling ensures graceful error responses, resource cleanup, and detailed error logging.

#### 3. Syntax
```python
try:
    # Code that might raise an exception
    risk_operation()
except SpecificException as e:
    # Code to handle exception
    handle_error(e)
else:
    # Code if no exception occurred
    on_success()
finally:
    # Code that runs regardless of outcome
    cleanup()
```

#### 4. Parameters
- `ExceptionClass`: The target exception type to catch (e.g. `ValueError`, `KeyError`, `HTTPException`).
- `as e`: Variable binding for the caught exception object instance.

#### 5. Examples
```python
def parse_age(raw_age):
    try:
        age = int(raw_age)
        if age < 0:
            raise ValueError("Age cannot be negative.")
        return age
    except (ValueError, TypeError) as err:
        print(f"Validation failed: {err}")
        return None
    finally:
        print("Parsing attempt completed.")
```

#### 6. Common Variations
- **Custom Exceptions**: Subclassing `Exception` or `werkzeug.exceptions.HTTPException` to create domain-specific application errors.
- **Reraising Exceptions**: Calling `raise` inside an `except` block to re-throw the caught exception after logging.

#### 7. Rules
- Never use a bare `except:` clause; always catch specific exception types.
- Order exception handlers from most specific to most general.

#### 8. Common Mistakes
- Catching `BaseException` or `Exception` blindly and swallowing errors silently without logging.

#### 9. Best Practices
- Keep `try` blocks as small as possible to isolate where errors originate.
- Combine with central error handlers in Flask (`@app.errorhandler`).

#### 10. Related Topics
Traceback, Custom Exceptions, HTTP Status Codes, Error Handlers.

---

## Phase 2: Flask Fundamentals

### 2.1 WSGI & Flask Core Architecture

#### 1. Definition
**WSGI (Web Server Gateway Interface)** is PEP 3333 standard specification describing how Python web applications communicate with web servers (like Gunicorn, uWSGI, or Nginx). Flask is a WSGI-compliant microframework built on top of Werkzeug (WSGI toolkit) and Jinja2 (template engine).

#### 2. Why It Exists
Decouples web servers from Python application code, allowing any WSGI app (Flask) to run seamlessly on any WSGI server without code changes.

#### 3. Syntax
```python
from flask import Flask

# Flask application instance (WSGI application entry point)
app = Flask(__name__)
```

#### 4. Parameters
- `import_name`: The name of the application module (`__name__`), used by Flask to locate templates, static files, and root paths.
- `static_folder`: Folder path serving static asset files (default: `'static'`).
- `template_folder`: Folder path storing Jinja2 HTML templates (default: `'templates'`).

#### 5. Examples
```python
from flask import Flask

app = Flask(__name__)

@app.route("/health")
def health_check():
    return {"status": "healthy", "service": "user-api"}, 200

# Entry point for WSGI server
application = app
```

#### 6. Common Variations
- **Application Factory**: Wrapping app instantiation inside a function `create_app()` for dynamic configuration and testing.

#### 7. Rules
- `Flask(__name__)` must be instantiated at module level or inside an application factory.
- WSGI servers expect a callable taking `(environ, start_response)`. Flask's `app` object implements this callable interface.

#### 8. Common Mistakes
- Passing a fixed string instead of `__name__`, causing issues when resolving relative package resources and paths.

#### 9. Best Practices
- Never use Flask's built-in development server (`app.run()`) in production; always use a WSGI server like Gunicorn (`gunicorn app:app`).

#### 10. Related Topics
Werkzeug, Gunicorn, Nginx, ASGI, App Factory.

---

### 2.2 App Setup & Development Server

#### 1. Definition
The entry point script and development web server used to launch, configure, and debug Flask web applications during local development.

#### 2. Why It Exists
Provides hot-reloading (automatically restarts on code changes) and an interactive browser-based debugger for rapid local development.

#### 3. Syntax
```python
from flask import Flask

app = Flask(__name__)

if __name__ == "__main__":
    app.run(host="127.0.0.1", port=5000, debug=True)
```

#### 4. Parameters
- `host`: IP address to listen on (`"127.0.0.1"` for localhost, `"0.0.0.0"` for public interface).
- `port`: Port number to listen on (default: `5000`).
- `debug`: Boolean enabling auto-reloader and interactive debugger.

#### 5. Examples
```python
import os
from flask import Flask

app = Flask(__name__)

@app.route("/")
def index():
    return "Flask API Operational"

if __name__ == "__main__":
    port = int(os.environ.get("PORT", 5000))
    app.run(host="0.0.0.0", port=port, debug=True)
```

#### 6. Common Variations
- **CLI Commands**: Launching via command line using `flask run` (reads `FLASK_APP` and `FLASK_ENV` environment variables).

#### 7. Rules
- Set `debug=False` in production to avoid exposing sensitive interactive shell debuggers to attackers.

#### 8. Common Mistakes
- Hardcoding sensitive configuration keys directly in application source code instead of environment variables.

#### 9. Best Practices
- Use `python-dotenv` and load configuration settings from `.env` files.

#### 10. Related Topics
`FLASK_APP`, Environment Variables, Gunicorn, Werkzeug Debugger.

---

### 2.3 Routing & URL Parameters

#### 1. Definition
Routing maps HTTP request URL paths to specific Python view functions. URL parameters allow capturing dynamic variable values from the request URL path.

#### 2. Why It Exists
Enables building dynamic RESTful endpoint paths (e.g. `/users/42` or `/orders/abc-123`) using clean, declarative decorators.

#### 3. Syntax
```python
@app.route("/path/<converter:variable_name>", methods=["GET", "POST"])
def view_function(variable_name):
    return f"Value: {variable_name}"
```

#### 4. Parameters
- `rule`: The URL pattern string (e.g. `"/users/<int:user_id>"`).
- `methods`: List of allowed HTTP methods (default: `["GET"]`).
- `converter`: Optional type converter (`string`, `int`, `float`, `path`, `uuid`).

#### 5. Examples
```python
from flask import Flask, jsonify

app = Flask(__name__)

@app.route("/api/v1/users/<int:user_id>", methods=["GET"])
def get_user(user_id):
    return jsonify({"user_id": user_id, "type": type(user_id).__name__}), 200

@app.route("/files/<path:file_path>")
def get_file(file_path):
    return f"Accessing file at: {file_path}"
```

#### 6. Common Variations
- `app.add_url_rule()`: Programmatic route registration alternative to `@app.route()` decorator.

#### 7. Rules
- URL converter variables must match the argument names declared in the decorated view function.
- Flask automatically appends trailing slashes according to canonical URL definitions.

#### 8. Common Mistakes
- Duplicate endpoint view function names in different routes (Flask view function names must be unique within an application/blueprint scope).

#### 9. Best Practices
- Use specific converters (`int:id`, `uuid:id`) to validate route parameter formats automatically at the router layer.

#### 10. Related Topics
HTTP Methods, Blueprints, URL Converters, `url_for`.

---

## Phase 3: Request & Response Handling

### 3.1 Request Object (Query Params, Body, Headers)

#### 1. Definition
The global proxy object `flask.request` exposing all incoming HTTP request data sent by the client, including headers, query string parameters, form payloads, raw/JSON request bodies, and files.

#### 2. Why It Exists
Provides a thread-safe unified API to parse and extract client data submitted during an HTTP request.

#### 3. Syntax
```python
from flask import request

# Accessing Query Parameters (?search=flask&page=1)
search_query = request.args.get("search", "")

# Accessing JSON Request Body
data = request.get_json()

# Accessing Form Data & Headers
form_val = request.form.get("key")
token = request.headers.get("Authorization")
```

#### 4. Parameters
- `request.args`: MultiDict of URL query parameters.
- `request.get_json(silent=True)`: Parses JSON payload (returns `None` if invalid when `silent=True`).
- `request.headers`: MultiDict of request HTTP headers.
- `request.files`: MultiDict of uploaded files (`FileStorage` objects).

#### 5. Examples
```python
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route("/api/search", methods=["POST"])
def search():
    # Query Param
    limit = request.args.get("limit", default=10, type=int)
    # Header
    api_key = request.headers.get("X-API-KEY")
    # JSON Body
    body = request.get_json() or {}
    
    return jsonify({
        "limit": limit,
        "query": body.get("query"),
        "key_present": api_key is not None
    }), 200
```

#### 6. Common Variations
- `request.data`: Accessing raw binary request body bytes.
- `request.is_json`: Boolean checking if `Content-Type` header is `application/json`.

#### 7. Rules
- `request` is a context-local proxy; it is only accessible inside active Flask request context blocks.

#### 8. Common Mistakes
- Calling `request.get_json()` when client sends non-JSON headers without setting `silent=True`, raising a `400 Bad Request`.
- Accessing `request.form` for JSON payloads (use `request.get_json()` instead).

#### 9. Best Practices
- Always validate incoming request payloads using validation schemas (like Marshmallow) before using data.

#### 10. Related Topics
`request.args`, `request.get_json()`, HTTP Headers, Thread Locals, Marshmallow.

---

### 3.2 Response Handling & jsonify

#### 1. Definition
View function returns in Flask can be strings, dicts/lists (auto-converted to JSON), or explicit `Response` objects generated via `jsonify()` or `make_response()`, alongside an optional HTTP status code and header dict.

#### 2. Why It Exists
Allows view functions to return structured HTTP responses, setting appropriate payload contents, status codes, and `Content-Type` headers (`application/json` vs `text/html`).

#### 3. Syntax
```python
from flask import jsonify, make_response

# Standard Tuple Return: (body, status_code, headers)
return jsonify({"status": "created"}), 201, {"X-Custom-Header": "Value"}
```

#### 4. Parameters
- `*args`, `**kwargs`: Data payload passed to `jsonify()` to serialize into JSON response body.
- `status_code`: Integer HTTP status code (default: `200`).
- `headers`: Dict of HTTP response headers.

#### 5. Examples
```python
from flask import Flask, jsonify, make_response

app = Flask(__name__)

@app.route("/api/items/<int:item_id>")
def get_item(item_id):
    if item_id > 100:
        return jsonify({"error": "Item not found"}), 404
    
    response = make_response(jsonify({"item_id": item_id, "name": "Widget"}))
    response.headers["Cache-Control"] = "max-age=3600"
    return response, 200
```

#### 6. Common Variations
- **Dict Auto-JSON**: Returning a raw Python dict/list directly from a view function automatically calls `jsonify()` in Flask 2.0+.

#### 7. Rules
- Returned response objects must conform to Flask's response tuple format: `(response, status, headers)` or `(response, status)` or `(response, headers)`.

#### 8. Common Mistakes
- Returning non-serializable objects (like raw database model instances) inside `jsonify()`.
- Returning status codes as strings instead of integers (`"200"` vs `200`).

#### 9. Best Practices
- Explicitly return proper standard HTTP status codes (`200 OK`, `201 Created`, `204 No Content`, `400 Bad Request`, `401 Unauthorized`, `404 Not Found`, `500 Error`).

#### 10. Related Topics
`make_response`, HTTP Status Codes, `jsonify`, Response Headers.

---

### 3.3 Cookies & Sessions

#### 1. Definition
**Cookies** are key-value string data stored directly on the client's browser by response headers. **Sessions** (`flask.session`) store user state server-side or in cryptographically signed, encrypted client-side cookies.

#### 2. Why It Exists
HTTP is a stateless protocol. Cookies and sessions allow web applications to remember user state (like logged-in user IDs or shopping cart items) across multiple consecutive HTTP requests.

#### 3. Syntax
```python
from flask import session, make_response

# Setting Flask Session
session["user_id"] = 42

# Reading Flask Session
user_id = session.get("user_id")

# Setting Cookie on Response
resp = make_response("Cookie set")
resp.set_cookie("theme", "dark", max_age=86400, httponly=True)
```

#### 4. Parameters
- `app.secret_key`: Cryptographic secret key required to sign session cookies.
- `set_cookie(key, value, max_age, httponly, secure, samesite)`: Cookie configuration options.

#### 5. Examples
```python
from flask import Flask, session, request, make_response, jsonify

app = Flask(__name__)
app.secret_key = "super-secret-key-change-in-production"

@app.route("/login", methods=["POST"])
def login():
    session["username"] = "alice"
    resp = make_response(jsonify({"msg": "Logged in"}))
    resp.set_cookie("user_preference", "dark_mode", httponly=True, secure=True)
    return resp

@app.route("/logout")
def logout():
    session.pop("username", None)
    return jsonify({"msg": "Logged out"})
```

#### 6. Common Variations
- **Server-Side Sessions**: Using `Flask-Session` to store session data in Redis, Memcached, or SQL databases instead of signed cookies.

#### 7. Rules
- Flask sessions **require** `app.secret_key` to be set; otherwise, accessing `session` raises a `RuntimeError`.
- Never store sensitive unencrypted data (passwords, secrets) directly in client-side cookies.

#### 8. Common Mistakes
- Assuming default Flask client-side session cookies are encrypted (they are only **signed**; users can inspect the cookie payload values!).

#### 9. Best Practices
- Always set `httponly=True` (prevents XSS access) and `secure=True` (requires HTTPS) when setting sensitive cookies.

#### 10. Related Topics
`SECRET_KEY`, Client-side vs Server-side Session, `Flask-Session`, Cookies.

---

## Phase 4: Templates & Static Files (Jinja2)

### 4.1 Jinja2 Templating & Inheritance

#### 1. Definition
Jinja2 is Flask's default templating engine that dynamically compiles HTML files using Python data variables, control logic (loops, conditionals), filters, and template inheritance (`{% extends %}`).

#### 2. Why It Exists
Separates presentation HTML layout from backend Python business logic, allowing reusable layout templates (header/footer/nav) across web pages.

#### 3. Syntax
```html
<!-- Base Template: base.html -->
<!DOCTYPE html>
<html>
  <body>
    {% block content %}{% endblock %}
  </body>
</html>

<!-- Child Template: index.html -->
{% extends "base.html" %} {% block content %}
<h1>Welcome, {{ user.name | title }}!</h1>
{% endblock %}
```

#### 4. Parameters
- `render_template(template_name, **context)`: Compiles Jinja template file with keyword variables.
- `{{ variable }}`: Renders output text.
- `{% logic %}`: Template control statements (`if`, `for`).

#### 5. Examples
```python
# app.py
from flask import Flask, render_template

app = Flask(__name__)

@app.route("/items")
def list_items():
    items = ["Apple", "Banana", "Cherry"]
    return render_template("items.html", items=items, title="Fruit Inventory")
```

```html
<!-- templates/items.html -->
{% extends "base.html" %}

{% block content %}
  <h2>{{ title }}</h2>
  <ul>
    {% for item in items %}
      <li>{{ item }}</li>
    {% else %}
      <li>No items found.</li>
    {% endfor %}
  </ul>
{% endblock %}
```

#### 6. Common Variations
- `url_for('static', filename='style.css')`: Generating static asset file URLs dynamically inside templates.

#### 7. Rules
- Template HTML files must be located inside the application's `templates/` directory.

#### 8. Common Mistakes
- Writing heavy database or business logic inside Jinja templates instead of view functions.

#### 9. Best Practices
- Use template inheritance (`base.html`) to maintain layout consistency.
- Avoid using `| safe` filter on raw user input to prevent Cross-Site Scripting (XSS) vulnerabilities.

#### 10. Related Topics
`render_template`, Jinja2 Filters, `url_for`, XSS, HTML.

---

## Phase 5: Databases & SQLAlchemy ORM

### 5.1 Flask-SQLAlchemy Models & CRUD

#### 1. Definition
**Flask-SQLAlchemy** is an extension providing a wrapper around SQLAlchemy ORM, mapping Python object classes to SQL relational database tables and executing CRUD operations using Python code.

#### 2. Why It Exists
Abstracts raw SQL queries, prevents SQL injection attacks via parameter binding, and provides object-oriented database interaction across SQLite, PostgreSQL, and MySQL.

#### 3. Syntax
```python
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()

class User(db.Model):
    __tablename__ = "users"
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
```

#### 4. Parameters
- `SQLALCHEMY_DATABASE_URI`: Connection string URI (e.g. `"sqlite:///app.db"` or `"postgresql://user:pass@localhost/dbname"`).
- `db.Column(type, primary_key, unique, nullable, default)`: Table column configuration attributes.

#### 5. Examples
```python
from flask import Flask, jsonify
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:///users.db"
db = SQLAlchemy(app)

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(50), nullable=False)

# CREATE
new_user = User(name="Bob")
db.session.add(new_user)
db.session.commit()

# READ
user = User.query.get_or_404(1)
all_users = User.query.filter_by(name="Bob").all()

# UPDATE
user.name = "Robert"
db.session.commit()

# DELETE
db.session.delete(user)
db.session.commit()
```

#### 6. Common Variations
- Modern SQLAlchemy 2.0 syntax using `db.session.execute(db.select(User))` instead of legacy `User.query`.

#### 7. Rules
- Always call `db.session.commit()` to persist pending `add` or `delete` changes to the database file/server.

#### 8. Common Mistakes
- Forgetting `db.session.commit()`, causing database changes to be lost when request ends.
- Not calling `db.session.rollback()` in `except` blocks when database errors occur during transactions.

#### 9. Best Practices
- Never use raw string formatting to construct SQL queries; always use ORM methods to prevent SQL Injection.

#### 10. Related Topics
SQLAlchemy, ORM, Connection Strings, SQLite, Transactions, CRUD.

---

### 5.2 Model Relationships (1:N & N:M)

#### 1. Definition
Defining foreign key constraints (`db.ForeignKey`) and ORM relationships (`db.relationship`) between tables to link models (One-to-Many, Many-to-Many).

#### 2. Why It Exists
Allows querying associated child/parent records through Python object attribute access (e.g. `user.posts` or `post.author`) without writing explicit JOIN queries.

#### 3. Syntax
```python
# One-to-Many (1:N)
class Parent(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    children = db.relationship("Child", backref="parent", lazy=True)

class Child(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    parent_id = db.Column(db.Integer, db.ForeignKey("parent.id"), nullable=False)
```

#### 4. Parameters
- `db.ForeignKey('table_name.id')`: Points column to foreign table's primary key.
- `db.relationship('ModelName', backref='ref_name', lazy=True)`: Connects models for navigation.
- `lazy='select'` / `'joined'` / `'dynamic'`: Controls when associated data is loaded from DB.

#### 5. Examples
```python
# One-to-Many (User -> Posts)
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(50), nullable=False)
    posts = db.relationship("Post", backref="author", lazy=True)

class Post(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
    user_id = db.Column(db.Integer, db.ForeignKey("user.id"), nullable=False)

# Usage
author = User(username="alice")
post1 = Post(title="First Post", author=author)
db.session.add_all([author, post1])
db.session.commit()

# Accessing related items
print(author.posts[0].title)  # "First Post"
print(post1.author.username) # "alice"
```

#### 6. Common Variations
- **Many-to-Many (N:M)**: Using an association table (`db.Table`) containing foreign keys of both target tables.

#### 7. Rules
- Foreign key table names inside `db.ForeignKey('user.id')` refer to SQL table names (`__tablename__`), while `db.relationship('User')` refers to Python Class names.

#### 8. Common Mistakes
- Confusing SQL table names (`'user.id'`) with Python class names inside `ForeignKey()`.

#### 9. Best Practices
- Use `lazy='select'` or `joined` appropriately to avoid N+1 query performance pitfalls.

#### 10. Related Topics
Foreign Keys, Joins, Cascades, N+1 Query Problem, Backref.

---

### 5.3 Database Migrations (Flask-Migrate / Alembic)

#### 1. Definition
**Flask-Migrate** is an extension that integrates **Alembic** schema migration tools into Flask, tracking changes made to SQLAlchemy models and generating incremental SQL schema migration scripts.

#### 2. Why It Exists
`db.create_all()` creates missing tables but cannot safely update existing table structures (e.g. adding a new column) without dropping and destroying existing production table data. Migrations allow updating DB schemas safely.

#### 3. Syntax
```bash
# Initialize migration environment (runs once)
flask db init

# Generate automatic migration script based on model edits
flask db migrate -m "Add email column to user"

# Apply pending migrations to database
flask db upgrade
```

#### 4. Parameters
- `-m "message"`: Descriptive message for the migration revision step.
- `flask db downgrade`: Reverts the latest applied schema migration.

#### 5. Examples
```python
# app.py
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate

app = Flask(__name__)
app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:///app.db"

db = SQLAlchemy(app)
migrate = Migrate(app, db) # Binds Flask app and SQLAlchemy db
```

#### 6. Common Variations
- Using Flask-Migrate inside Application Factories (`migrate.init_app(app, db)`).

#### 7. Rules
- Always review automatically generated Alembic migration scripts before executing `flask db upgrade`.

#### 8. Common Mistakes
- Using `db.create_all()` in production alongside Flask-Migrate, causing migration tracking drift.

#### 9. Best Practices
- Check migration script files into Git revision control.

#### 10. Related Topics
Alembic, Schema Evolution, DDL, Revision History.

---

## Phase 6: Modular Applications (Flask Blueprints)

### 6.1 Flask Blueprints

#### 1. Definition
A **Blueprint** is a Flask object that organizes related application routes, templates, static files, and error handlers into modular, reusable component packages.

#### 2. Why It Exists
Prevents creating monolithic 2,000-line single `app.py` files by splitting large applications into logical domain modules (e.g. `auth`, `users`, `products`, `orders`).

#### 3. Syntax
```python
# In module: auth/routes.py
from flask import Blueprint

auth_bp = Blueprint("auth", __name__, url_prefix="/auth")

@auth_bp.route("/login")
def login():
    return "Login Page"

# In main: app.py
from auth.routes import auth_bp
app.register_blueprint(auth_bp)
```

#### 4. Parameters
- `name`: Unique internal string identifier for the blueprint (used in `url_for('auth.login')`).
- `import_name`: Module package location (`__name__`).
- `url_prefix`: Optional path prefix prepended to all routes defined under this blueprint (e.g. `"/api/v1/auth"`).

#### 5. Examples
```
# Project Directory Structure
my_project/
├── app/
│   ├── __init__.py
│   ├── auth/
│   │   ├── __init__.py
│   │   └── routes.py
│   └── main/
│       ├── __init__.py
│       └── routes.py
└── run.py
```

```python
# app/auth/routes.py
from flask import Blueprint, jsonify

auth_bp = Blueprint("auth", __name__, url_prefix="/api/v1/auth")

@auth_bp.route("/register", methods=["POST"])
def register():
    return jsonify({"msg": "Registered successfully"}), 201
```

```python
# app/__init__.py
from flask import Flask
from app.auth.routes import auth_bp

def create_app():
    app = Flask(__name__)
    app.register_blueprint(auth_bp)
    return app
```

#### 6. Common Variations
- Registering blueprints with dynamic `url_prefix` settings during runtime.

#### 7. Rules
- Route endpoint references with `url_for()` must include the blueprint prefix: `url_for('blueprint_name.view_function_name')`.

#### 8. Common Mistakes
- Circular imports between blueprint route files and main application instances (solve using Application Factory pattern).

#### 9. Best Practices
- Organize large applications by feature modules (`auth/`, `billing/`, `users/`) rather than layer type (`controllers/`, `views/`).

#### 10. Related Topics
Application Factory, Project Structure, `url_for`, Modular Architecture.

---

## Phase 7: Authentication & Authorization

### 7.1 Password Hashing (Werkzeug)

#### 1. Definition
The process of transforming plain-text passwords into secure, non-reversible cryptographic hash strings using salted key-derivation algorithms (`pbkdf2:sha256` or `scrypt`).

#### 2. Why It Exists
Storing plain-text passwords in databases is a catastrophic security failure. Hashing ensures user passwords cannot be recovered even if the database is breached.

#### 3. Syntax
```python
from werkzeug.security import generate_password_hash, check_password_hash

# Hash password
hashed_pw = generate_password_hash("my_secret_password")

# Verify password
is_valid = check_password_hash(hashed_pw, "user_input_password")
```

#### 4. Parameters
- `password`: Raw plain-text password string.
- `method`: Hash algorithm specification (default: `'pbkdf2:sha256'`).
- `salt_length`: Integer salt length.

#### 5. Examples
```python
from flask_sqlalchemy import SQLAlchemy
from werkzeug.security import generate_password_hash, check_password_hash

db = SQLAlchemy()

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    password_hash = db.Column(db.String(256), nullable=False)

    def set_password(self, password):
        self.password_hash = generate_password_hash(password)

    def check_password(self, password):
        return check_password_hash(self.password_hash, password)
```

#### 6. Common Variations
- Using external hashing libraries like `bcrypt` or `argon2-cffi` via `Flask-Bcrypt`.

#### 7. Rules
- Never log raw plain-text passwords in application logs or return them in API responses.

#### 8. Common Mistakes
- Using outdated insecure hash algorithms (MD5, SHA1) without salt.

#### 9. Best Practices
- Always use built-in Werkzeug helpers or `bcrypt` which handle random salting automatically.

#### 10. Related Topics
Werkzeug Security, Hashing, Salt, Passwords, `Flask-Login`.

---

### 7.2 JWT Authentication (Flask-JWT-Extended)

#### 1. Definition
**JSON Web Tokens (JWT)** are compact, URL-safe, cryptographically signed tokens containing user identity claims (e.g. `user_id`), used for stateless authentication in REST APIs.

#### 2. Why It Exists
Allows mobile clients, single-page apps (React/Vue), and microservices to authenticate API requests without requiring server-side session storage.

#### 3. Syntax
```python
from flask_jwt_extended import create_access_token, jwt_required, get_jwt_identity

# Create Token
access_token = create_access_token(identity=str(user.id))

# Protect Route
@app.route("/protected")
@jwt_required()
def protected_route():
    current_user_id = get_jwt_identity()
    return {"user_id": current_user_id}
```

#### 4. Parameters
- `JWT_SECRET_KEY`: Secret key used to sign tokens.
- `expires_delta`: `datetime.timedelta` setting token lifespan (e.g. 15 minutes).
- `headers={"Authorization": "Bearer <token>"}`: Standard HTTP header format.

#### 5. Examples
```python
from datetime import timedelta
from flask import Flask, jsonify, request
from flask_jwt_extended import JWTManager, create_access_token, jwt_required, get_jwt_identity

app = Flask(__name__)
app.config["JWT_SECRET_KEY"] = "jwt-secret-key-change-me"
app.config["JWT_ACCESS_TOKEN_EXPIRES"] = timedelta(hours=1)
jwt = JWTManager(app)

@app.route("/api/login", methods=["POST"])
def login():
    username = request.json.get("username")
    password = request.json.get("password")
    
    if username == "admin" and password == "secret":
        token = create_access_token(identity=username)
        return jsonify(access_token=token), 200
    return jsonify({"msg": "Bad credentials"}), 401

@app.route("/api/profile")
@jwt_required()
def profile():
    current_user = get_jwt_identity()
    return jsonify(logged_in_as=current_user), 200
```

#### 6. Common Variations
- **Dual Token Architecture**: Issuing short-lived Access Tokens (15 min) alongside long-lived Refresh Tokens (7 days) for seamless token renewal.

#### 7. Rules
- Clients must include the token in the request header: `Authorization: Bearer <access_token>`.

#### 8. Common Mistakes
- Storing sensitive, unencrypted private user data inside public JWT payload claims.

#### 9. Best Practices
- Keep access token lifespan short and implement token revocation blocklists for logged-out tokens.

#### 10. Related Topics
JWT, Stateless Auth, `Authorization` Header, Bearer Tokens, Refresh Tokens.

---

## Phase 8: REST API Development

### 8.1 Schema Validation & Serialization (Marshmallow)

#### 1. Definition
**Marshmallow** is an ORM/framework-agnostic library used to convert complex datatypes (like ORM models) to and from native Python datatypes, performing strict input data validation during the process.

#### 2. Why It Exists
Automates validating incoming HTTP request JSON payloads and serializing output database model objects into JSON responses cleanly.

#### 3. Syntax
```python
from marshmallow import Schema, fields, validate

class UserSchema(Schema):
    id = fields.Int(dump_only=True)
    username = fields.Str(required=True, validate=validate.Length(min=3))
    email = fields.Email(required=True)
```

#### 4. Parameters
- `required=True`: Field must be present during deserialization.
- `dump_only=True`: Field only included in output serialization (e.g. auto-increment `id`).
- `load_only=True`: Field only accepted in input payload (e.g. `password`).

#### 5. Examples
```python
from marshmallow import Schema, fields, ValidationError

class PostSchema(Schema):
    id = fields.Int(dump_only=True)
    title = fields.Str(required=True)
    content = fields.Str(required=True)

post_schema = PostSchema()
posts_schema = PostSchema(many=True)

# Deserialization & Validation (Load)
try:
    data = post_schema.load({"title": "Flask Marshmallow"})
except ValidationError as err:
    print(err.messages)  # Returns dict of validation errors

# Serialization (Dump)
json_output = post_schema.dump(my_post_object)
```

#### 6. Common Variations
- `marshmallow-sqlalchemy`: Automatically generating Marshmallow schema fields directly from SQLAlchemy model classes (`SQLAlchemyAutoSchema`).

#### 7. Rules
- Use `.load()` for deserializing and validating input payloads; use `.dump()` for serializing output objects.

#### 8. Common Mistakes
- Forgetting `many=True` when serializing a list of multiple database record objects.

#### 9. Best Practices
- Centralize input request validation with schema instances to ensure API endpoints receive sanitized, type-safe inputs.

#### 10. Related Topics
Marshmallow, Validation, Serialization, DTOs, Flask-Marshmallow.

---

### 8.2 Error Handling & Custom HTTP Exception Responses

#### 1. Definition
Using Flask's `@app.errorhandler` or `@blueprint.errorhandler` decorators to catch standard HTTP errors and uncaught application exceptions, returning consistent JSON error payload structures.

#### 2. Why It Exists
Ensures API clients receive clean, uniform JSON error messages (e.g. `{"error": "Resource Not Found", "status": 404}`) instead of default raw HTML error pages.

#### 3. Syntax
```python
@app.errorhandler(404)
def not_found_error(error):
    return jsonify({"error": "Resource Not Found", "status": 404}), 404
```

#### 4. Parameters
- `code_or_exception`: HTTP status integer (e.g., `404`, `500`) or Exception class (`HTTPException`, `ValidationError`).

#### 5. Examples
```python
from flask import Flask, jsonify
from werkzeug.exceptions import HTTPException
from marshmallow import ValidationError

app = Flask(__name__)

@app.errorhandler(ValidationError)
def handle_marshmallow_validation(err):
    return jsonify({"error": "Validation Failed", "messages": err.messages}), 422

@app.errorhandler(HTTPException)
def handle_http_exception(err):
    return jsonify({
        "error": err.name,
        "description": err.description,
        "status": err.code
    }), err.code

@app.errorhandler(Exception)
def handle_generic_exception(err):
    # Log actual traceback internally here
    return jsonify({"error": "Internal Server Error", "status": 500}), 500
```

#### 6. Common Variations
- Custom HTTP Exception classes inheriting from `werkzeug.exceptions.HTTPException` with default status code definitions.

#### 7. Rules
- Always return appropriate standard HTTP status codes alongside error JSON payloads (`400`, `401`, `403`, `404`, `422`, `500`).

#### 8. Common Mistakes
- Exposing raw Python internal exception tracebacks to API clients in production `500` error responses.

#### 9. Best Practices
- Maintain a single, standard JSON error response schema across all endpoints in the application.

#### 10. Related Topics
`errorhandler`, `HTTPException`, ValidationError, JSON API Standards.

---

## Phase 9: Advanced Flask Architecture

### 9.1 Application Factory Pattern

#### 1. Definition
Encapsulating Flask application instantiation, configuration loading, extension registration, and blueprint registering inside a callable factory function (typically named `create_app()`).

#### 2. Why It Exists
Eliminates global app variable state, prevents circular imports, and allows instantiating multiple isolated app instances with different configurations (e.g. `DevelopmentConfig`, `TestingConfig`, `ProductionConfig`).

#### 3. Syntax
```python
def create_app(config_class=None):
    app = Flask(__name__)
    app.config.from_object(config_class)
    
    # Initialize extensions
    db.init_app(app)
    
    # Register blueprints
    app.register_blueprint(main_bp)
    
    return app
```

#### 4. Parameters
- `config_class` / `config_name`: Object class or string file path specifying configuration environment settings.

#### 5. Examples
```python
# config.py
class Config:
    SECRET_KEY = "dev-key"
    SQLALCHEMY_TRACK_MODIFICATIONS = False

class DevelopmentConfig(Config):
    SQLALCHEMY_DATABASE_URI = "sqlite:///dev.db"

class TestingConfig(Config):
    TESTING = True
    SQLALCHEMY_DATABASE_URI = "sqlite:///:memory:"

# app/__init__.py
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()

def create_app(config_object="config.DevelopmentConfig"):
    app = Flask(__name__)
    app.config.from_object(config_object)

    db.init_app(app)

    from app.routes import api_bp
    app.register_blueprint(api_bp)

    return app
```

#### 6. Common Variations
- Factory function accepting string names like `create_app('testing')` dynamically selected via environment variables.

#### 7. Rules
- Extensions (like `db = SQLAlchemy()`) must be instantiated globally *without* passing the `app` instance, and initialized inside the factory using `extension.init_app(app)`.

#### 8. Common Mistakes
- Instantiating extensions globally with `db = SQLAlchemy(app)` inside the factory module, leading to circular imports.

#### 9. Best Practices
- Use Application Factory pattern as the default standard setup for all non-trivial Flask applications.

#### 10. Related Topics
App Factory, `init_app`, Circular Imports, Config Management.

---

### 9.2 Middleware & Hooks (@app.before_request)

#### 1. Definition
Flask request lifecycle hooks (`@app.before_request`, `@app.after_request`, `@app.teardown_request`) allow executing code handlers automatically before or after every HTTP request.

#### 2. Why It Exists
Enables executing centralized request processing logic across all endpoints, such as database connection setup, request timing, CORS headers, logging, and global rate limiting.

#### 3. Syntax
```python
@app.before_request
def before_request_func():
    # Executed before reaching view function
    pass

@app.after_request
def after_request_func(response):
    # Executed after view function returns
    return response
```

#### 4. Parameters
- `response`: The `Response` object passed to `@app.after_request`, which must be returned.

#### 5. Examples
```python
import time
from flask import Flask, request, g

app = Flask(__name__)

@app.before_request
def start_timer():
    g.start_time = time.time()

@app.after_request
def add_process_time_header(response):
    if hasattr(g, "start_time"):
        elapsed = time.time() - g.start_time
        response.headers["X-Process-Time"] = f"{elapsed:.4f}s"
    return response
```

#### 6. Common Variations
- `app.wsgi_app`: Wrapping WSGI app directly with custom WSGI middleware classes (e.g. `ProxyFix`).

#### 7. Rules
- `@app.after_request` **must** accept a `response` object argument and **must** return a `response` object.
- If `@app.before_request` returns a value (other than `None`), request execution stops immediately and that return value is used as the response.

#### 8. Common Mistakes
- Forgetting to return `response` from an `@app.after_request` hook, breaking HTTP processing with a `TypeError`.

#### 9. Best Practices
- Use `flask.g` proxy object to store request-scoped data during lifecycle hooks.

#### 10. Related Topics
`before_request`, `after_request`, `flask.g`, WSGI Middleware.

---

### 9.3 Background Tasks (Celery Integration)

#### 1. Definition
**Celery** is an asynchronous task queue system that offloads heavy, long-running computations (e.g. sending emails, generating reports, processing images) out of the HTTP request-response cycle into background worker processes.

#### 2. Why It Exists
Prevents blocking HTTP request view functions, maintaining sub-100ms API response times for web clients.

#### 3. Syntax
```python
from celery import Celery

celery = Celery(app.name, broker="redis://localhost:6379/0")

@celery.task
def async_task(arg):
    # Long running background logic
    return "Done"
```

#### 4. Parameters
- `broker`: Message broker URL (e.g. Redis or RabbitMQ) transferring tasks to workers.
- `backend`: Result store URL storing task execution outcomes.

#### 5. Examples
```python
from flask import Flask, jsonify
from celery import Celery

def make_celery(app):
    celery = Celery(
        app.import_name,
        backend=app.config["CELERY_RESULT_BACKEND"],
        broker=app.config["CELERY_BROKER_URL"]
    )
    celery.conf.update(app.config)
    return celery

app = Flask(__name__)
app.config["CELERY_BROKER_URL"] = "redis://localhost:6379/0"
app.config["CELERY_RESULT_BACKEND"] = "redis://localhost:6379/0"
celery = make_celery(app)

@celery.task
def send_async_email(email_address):
    # Simulated long task
    import time; time.sleep(5)
    return f"Email sent to {email_address}"

@app.route("/send-email", methods=["POST"])
def trigger_email():
    # Enqueue task non-blockingly
    send_async_email.delay("user@example.com")
    return jsonify({"msg": "Email task queued!"}), 202
```

#### 6. Common Variations
- Using **RQ (Redis Queue)** as a lightweight Python alternative to Celery.

#### 7. Rules
- Launch task execution using `.delay(*args)` or `.apply_async()` to trigger background execution asynchronously.

#### 8. Common Mistakes
- Attempting to pass complex, non-serializable database model objects as arguments to `.delay()` (pass object primary key IDs instead!).

#### 9. Best Practices
- Pass primitive IDs (`user_id=42`) as task arguments and query the database fresh inside the Celery worker process.

#### 10. Related Topics
Celery, Redis, Message Brokers, Asynchronous Tasks, `.delay()`.

---

### 9.4 CORS & Rate Limiting

#### 1. Definition
**CORS (Cross-Origin Resource Sharing)** allows browser apps hosted on different domains (e.g. `http://localhost:3000`) to access Flask API endpoints securely using `Flask-CORS`. **Rate Limiting** (`Flask-Limiter`) caps the number of requests a client IP can make within a given time window.

#### 2. Why It Exists
- CORS protects users by bypassing browser Same-Origin Policy restrictions safely for trusted client domains.
- Rate limiting protects APIs from abuse, scraping, brute-force login attacks, and Denial of Service (DoS).

#### 3. Syntax
```python
from flask_cors import CORS
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address

# Setup CORS
CORS(app, resources={r"/api/*": {"origins": "https://myfrontend.com"}})

# Setup Rate Limiter
limiter = Limiter(get_remote_address, app=app, default_limits=["200 per day", "50 per hour"])
```

#### 4. Parameters
- `origins`: String or list of allowed origin URL domains.
- `default_limits`: List of global request threshold rules (e.g., `"5 per minute"`).

#### 5. Examples
```python
from flask import Flask, jsonify
from flask_cors import CORS
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address

app = Flask(__name__)

# Enable CORS for frontend application domain
CORS(app, resources={r"/api/*": {"origins": "*"}})

# Enable Rate Limiting by Client IP
limiter = Limiter(
    key_func=get_remote_address,
    app=app,
    default_limits=["100 per hour"]
)

@app.route("/api/login", methods=["POST"])
@limiter.limit("5 per minute")  # Strict limit for login endpoint
def login():
    return jsonify({"status": "attempted"})
```

#### 6. Common Variations
- Storing rate limiter key counters in external shared Redis stores for multi-worker production setups (`storage_uri="redis://localhost:6379"`).

#### 7. Rules
- Browser clients automatically execute `OPTIONS` preflight requests prior to cross-origin POST/PUT requests; CORS headers must allow `OPTIONS`.

#### 8. Common Mistakes
- Setting CORS origins to wildcard `"*"` in production while allowing credentials (`supports_credentials=True`), violating browser security specs.

#### 9. Best Practices
- Apply strict rate limits to sensitive routes like authentication (`/login`, `/register`) and password reset endpoints.

#### 10. Related Topics
CORS, Same-Origin Policy, `Flask-CORS`, `Flask-Limiter`, Redis, Security.

---

## ⚡ Quick Reference Cheat Sheets

<details><summary><b>📘 Flask Beginner Basics & Architecture Flow</b></summary>

### What is Flask?
Flask is a **lightweight Python web framework** used to build web applications, REST APIs, ML backends, chatbots, file upload services, and authentication systems. Unlike Django, Flask gives you only essentials and lets you select extra libraries as needed.

### 1. Minimal App Structure
```python
from flask import Flask, request, jsonify, render_template, redirect, url_for, session

app = Flask(__name__)
app.secret_key = "super-secret-key"

@app.route("/")
def home():
    return "Hello, Flask!"

if __name__ == "__main__":
    app.run(debug=True)
```

### 2. URL Parameter Converters
| Converter | Example Path | Output Type |
|---|---|---|
| `<string:name>` | `/user/<name>` | `str` (default) |
| `<int:id>` | `/add/<int:a>/<int:b>` | `int` |
| `<float:price>` | `/item/<float:price>` | `float` |
| `<path:file_path>` | `/files/<path:p>` | `str` (allows slashes `/`) |
| `<uuid:id>` | `/orders/<uuid:id>` | `uuid.UUID` |

### 3. HTTP Methods Overview
| Method | Purpose | Typical Response Code |
|---|---|---|
| `GET` | Read data | `200 OK` |
| `POST` | Create new data | `201 Created` |
| `PUT` | Complete update | `200 OK` |
| `PATCH` | Partial update | `200 OK` |
| `DELETE` | Delete resource | `204 No Content` |

### 4. Typical Request-Response Flow Diagram
```
Browser / Postman Client
        │
        ▼ (HTTP Request)
Flask Router (@app.route)
        │
        ▼
Extract Data (request.args / request.form / request.json / request.files)
        │
        ▼
Business Logic (Database / ORM / AI Service)
        │
        ▼
Prepare Response (jsonify() / render_template() / redirect())
        │
        ▼ (HTTP Response 200/201/400/404)
Client Receives Payload
```

### 5. Frequently Used Flask Core Objects
| Object | Purpose |
|---|---|
| `Flask(__name__)` | Creates the core WSGI application instance |
| `@app.route()` | Maps a URL path pattern to a Python view function |
| `request` | Thread-local proxy reading incoming client payload data |
| `jsonify()` | Serializes dicts/lists to JSON with `application/json` header |
| `render_template()` | Compiles Jinja2 HTML templates from `templates/` folder |
| `redirect()` | Redirects client browser to a target URL path |
| `url_for()` | Generates URL paths dynamically from view function names |
| `session` | Stores cryptographically signed user session state |

</details>

<details><summary><b>📊 Flask Request Data & Content-Type Cheat Sheet</b></summary>

Flask parses incoming client request payloads into specific `request` proxy attributes based on the **`Content-Type`** HTTP header sent by the client.

### Content-Type Mapping Table
| Client `Content-Type` Header | Flask Data Attribute | Use Case |
|---|---|---|
| `application/json` | `request.json` / `request.get_json()` | JSON APIs (React, Mobile, Postman) |
| `multipart/form-data` | `request.form` + `request.files` | HTML forms containing file upload inputs |
| `application/x-www-form-urlencoded` | `request.form` | Standard HTML form submissions |
| Any (URL Query String) | `request.args` | URL parameters (e.g. `/search?page=2&limit=10`) |

---

### Request Data Extraction Examples

#### 1. JSON Payload (`request.json`)
```python
# Client: POST /api/query (Content-Type: application/json)
# Payload: {"question": "What is AI?", "user": "Alice"}
data = request.get_json()
question = data.get("question")
user = data.get("user")
```

#### 2. Form Data & Files (`request.form` & `request.files`)
```python
# Client: POST /upload (Content-Type: multipart/form-data)
username = request.form.get("username")      # Text input
resume = request.files.get("resume")         # Uploaded FileStorage object
resume.save(f"./uploads/{resume.filename}")
```

#### 3. Duplicate Form Field Names (`request.form.getlist`)
When an HTML form submits multiple checkboxes or inputs sharing the same `name`:
```html
<input type="checkbox" name="skills" value="Python">
<input type="checkbox" name="skills" value="Java">
```
```python
# Use getlist() to retrieve ALL selected values as a list:
skills_list = request.form.getlist("skills")  # Output: ["Python", "Java"]
```

---

### Key Rules to Remember
1. **`request.json`**: Use for JSON REST APIs.
2. **`request.form`**: Use for text fields from HTML forms.
3. **`request.files`**: Use for binary file uploads (`FileStorage` objects).
4. **Duplicate Keys in JSON**: Invalid; standard JSON parser retains only the last key-value pair.
5. **Form vs Files Same Name**: `request.form["file"]` and `request.files["file"]` are stored in separate collections.

</details>

<details><summary><b>🚦 HTTP Status Codes Master Reference (Flask & FastAPI)</b></summary>

Comprehensive status code decision matrix used for 95% of web application and REST API development.

### Master Status Code Table
| Status Code | Standard Name | When to Use | Example Code (Flask) | Example Code (FastAPI) |
|---|---|---|---|---|
| **200** | OK | Successful GET/PUT query | `return jsonify(data), 200` | `return {"data": data}` |
| **201** | Created | Resource successfully created | `return jsonify(data), 201` | `@app.post(..., status_code=201)` |
| **204** | No Content | Successful DELETE (no response body) | `return "", 204` | `return Response(status_code=204)` |
| **400** | Bad Request | Client sent malformed JSON/params | `return jsonify(error="Bad Request"), 400` | `raise HTTPException(400, "Bad Request")` |
| **401** | Unauthorized | Unauthenticated / missing JWT token | `return jsonify(error="Unauthorized"), 401` | `raise HTTPException(401, "Invalid token")` |
| **403** | Forbidden | Authenticated but lacks role permissions | `return jsonify(error="Forbidden"), 403` | `raise HTTPException(403, "Forbidden")` |
| **404** | Not Found | Target ID or URL path does not exist | `return jsonify(error="Not Found"), 404` | `raise HTTPException(404, "Not Found")` |
| **409** | Conflict | Duplicate record (e.g. email exists) | `return jsonify(error="Conflict"), 409` | `raise HTTPException(409, "Email exists")` |
| **422** | Unprocessable Entity | Valid JSON format, but validation failed | `return jsonify(error="Invalid data"), 422` | Automatically raised by Pydantic |
| **500** | Internal Server Error | Unhandled server exception / DB failure | `return jsonify(error="Server Error"), 500` | `raise HTTPException(500, "Server Error")` |

---

### Quick Status Code Decision Guide
- **Success**: `200` (OK), `201` (Created), `204` (No Content / Deleted).
- **Client Error**: `400` (Malformed), `401` (Not Logged In), `403` (No Permission), `404` (Missing), `409` (Duplicate), `422` (Validation Failed).
- **Server Error**: `500` (Crash / Exception).

</details>

