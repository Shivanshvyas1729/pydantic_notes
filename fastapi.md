# ⚡ Complete FastAPI Master Note & Fast-Learning Cheat Sheet

---

## 📋 Table of Contents

1. [Introduction to FastAPI & ASGI Architecture](#1-introduction-to-fastapi--asgi-architecture)
2. [Project Setup & ASGI Server (Uvicorn)](#2-project-setup--asgi-server-uvicorn)
3. [Routing & HTTP Methods (GET, POST, PUT, PATCH, DELETE)](#3-routing--http-methods-get-post-put-patch-delete)
4. [Path Parameters & Type Converters](#4-path-parameters--type-converters)
5. [Query Parameters & Default Constraints](#5-query-parameters--default-constraints)
6. [Request Body & Pydantic Validation Models](#6-request-body--pydantic-validation-models)
7. [Response Models & Data Filtering](#7-response-models--data-filtering)
8. [HTTP Status Codes & Response Customization](#8-http-status-codes--response-customization)
9. [Dependency Injection System (`Depends`)](#9-dependency-injection-system-depends)
10. [Error Handling & `HTTPException`](#10-error-handling--httpexception)
11. [Form Data & File Uploads (`UploadFile` & `File`)](#11-form-data--file-uploads-uploadfile--file)
12. [Authentication & Security (JWT & OAuth2)](#12-authentication--security-jwt--oauth2)
13. [Database Integration (SQLAlchemy Async ORM / SQLModel)](#13-database-integration-sqlalchemy-async-orm--sqlmodel)
14. [Asynchronous Programming (`async` / `await`)](#14-asynchronous-programming-async--await)
15. [Middleware & Request Lifecycle Hooks](#15-middleware--request-lifecycle-hooks)
16. [Background Tasks (`BackgroundTasks`)](#16-background-tasks-backgroundtasks)
17. [Modular Architecture (`APIRouter`)](#17-modular-architecture-apirouter)
18. [Automated Testing (`TestClient` & Pytest)](#18-automated-testing-testclient--pytest)
19. [Production Deployment (Docker, Gunicorn + Uvicorn Workers, Nginx)](#19-production-deployment-docker-gunicorn--uvicorn-workers-nginx)

---

## 1. Introduction to FastAPI & ASGI Architecture

### 1. What it is
FastAPI is a modern, high-performance, asynchronous Python web framework built on top of **Starlette** (for web routing and ASGI concurrency) and **Pydantic** (for data validation, type casting, and schema generation).

### 2. Why it exists
Traditional WSGI frameworks (Flask/Django) execute synchronously and lack native type safety or auto-generated OpenAPI documentation. FastAPI provides `async`/`await` performance (comparable to Node.js/Go), auto-generated Swagger UI docs at `/docs`, and zero-boilerplate Pydantic type validation.

### 3. How it works
FastAPI processes incoming requests via ASGI (Asynchronous Server Gateway Interface). It parses HTTP payloads through Pydantic schemas, converts parameters based on standard Python type hints, runs async handlers on an event loop, and serializes output responses to JSON.

### 4. Minimal example
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def root():
    return {"message": "FastAPI is operational"}
```

### 5. One exercise
**Exercise**: Create a GET endpoint `/health` returning `{"status": "ok", "version": "1.0.0"}` and inspect the interactive Swagger UI documentation at `http://127.0.0.1:8000/docs`.

### 6. One common mistake
Treating FastAPI like a synchronous framework by running heavy blocking CPU/disk operations directly inside `async def` functions, freezing the event loop for all concurrent users.

### 7. One best practice
Always declare Python type hints (`int`, `str`, `EmailStr`, `BaseModel`) on view function parameters to get automatic data validation, IDE autocompletion, and OpenAPI docs.

---

## 2. Project Setup & ASGI Server (Uvicorn)

### 1. What it is
Setting up a Python virtual environment (`venv`), installing `fastapi` and `uvicorn`, and executing an **ASGI server** (`uvicorn`) to serve the asynchronous application.

### 2. Why it exists
FastAPI is an application framework, not an HTTP web server. ASGI servers like Uvicorn provide the event loop infrastructure necessary to process asynchronous network sockets.

### 3. How it works
Uvicorn listens on a socket port, translates HTTP wire bytes into ASGI message dictionaries, invokes the FastAPI application callable, and streams response data back across the socket.

### 4. Minimal example
```bash
# 1. Environment Setup
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
pip install "fastapi[standard]" uvicorn

# 2. Launch Uvicorn Development Server with auto-reload
uvicorn main:app --reload --port 8000
```

### 5. One exercise
**Exercise**: Launch a FastAPI application in `main.py` using `uvicorn main:app --reload --port 8080` and verify hot-reloading by adding a new route.

### 6. One common mistake
Forgetting the `--reload` flag during local development, leading to confusion when code updates are not reflected in live API responses.

### 7. One best practice
Use `pip install "fastapi[standard]"` to install high-performance C-based dependencies like `uvloop` (fast event loop) and `httptools` (fast HTTP parser).

---

## 3. Routing & HTTP Methods (GET, POST, PUT, PATCH, DELETE)

### 1. What it is
Mapping URL path patterns and specific HTTP request verbs (`GET`, `POST`, `PUT`, `PATCH`, `DELETE`) to Python view functions using decorator methods on the `FastAPI` app instance (`@app.get()`, `@app.post()`, etc.).

### 2. Why it exists
Enables building RESTful APIs where different HTTP verbs on the same URL path execute distinct CRUD (Create, Read, Update, Delete) actions.

### 3. HTTP Methods Summary
| Method | Decorator | Purpose | Typical Action | Response Code |
|---|---|---|---|---|
| `GET` | `@app.get()` | Read data | Fetch user list or profile | `200 OK` |
| `POST` | `@app.post()` | Create new data | Register user or submit form | `201 Created` |
| `PUT` | `@app.put()` | Replace resource | Complete document update | `200 OK` |
| `PATCH` | `@app.patch()` | Partial update | Update specific fields (e.g. email) | `200 OK` |
| `DELETE` | `@app.delete()` | Remove resource | Delete user record | `204 No Content` |

### 4. Minimal example
```python
from fastapi import FastAPI, status

app = FastAPI()

@app.get("/users")
async def list_users():
    return [{"id": 1, "username": "alice"}]

@app.post("/users", status_code=status.HTTP_201_CREATED)
async def create_user(user_data: dict):
    return {"status": "created", "user": user_data}

@app.put("/users/{user_id}")
async def update_user(user_id: int, user_data: dict):
    return {"status": "replaced", "id": user_id, "data": user_data}

@app.patch("/users/{user_id}")
async def patch_user(user_id: int, email: str):
    return {"status": "updated", "id": user_id, "new_email": email}

@app.delete("/users/{user_id}", status_code=status.HTTP_204_NO_CONTENT)
async def delete_user(user_id: int):
    return None
```

### 5. One exercise
**Exercise**: Build an API with 5 routes for `/products` (`GET`, `POST`, `PUT`, `PATCH`, `DELETE`) that return JSON payloads with their respective HTTP method names.

### 6. One common mistake
Using `@app.get()` for endpoints that create or modify database state, violating HTTP REST idempotency standards.

### 7. One best practice
Always set appropriate default success status codes on decorators (e.g. `@app.post(..., status_code=status.HTTP_201_CREATED)`).

---

## 4. Path Parameters & Type Converters

### 1. What it is
Dynamic path variables declared inside curly braces inside the route string (e.g. `/users/{user_id}`) that extract values from the URL path and cast them to Python types.

### 2. Why it exists
Enables targeting specific individual resources by ID while enforcing automatic type validation at the router level.

### 3. How it works
FastAPI extracts the path variable `{user_id}`, converts it to the declared type (`int`, `str`, `float`, `UUID`), and passes it to the view function. If type validation fails, FastAPI returns an automatic `422 Unprocessable Entity` JSON response.

### 4. Minimal example
```python
from uuid import UUID
from fastapi import FastAPI, Path

app = FastAPI()

@app.get("/items/{item_id}")
async def read_item(item_id: int = Path(..., gt=0, description="Item ID must be > 0")):
    return {"item_id": item_id, "type": type(item_id).__name__}

@app.get("/orders/{order_uuid}")
async def read_order(order_uuid: UUID):
    return {"order_uuid": str(order_uuid)}
```

### 5. One exercise
**Exercise**: Create a path parameter endpoint `/products/{product_id}` where `product_id` must be an integer between 1 and 500. Verify that passing string `"abc"` returns a `422` error.

### 6. One common mistake
Declaring static paths *after* dynamic path routes (e.g. placing `@app.get("/users/me")` below `@app.get("/users/{user_id}")`), causing `"me"` to be matched as a string `user_id`.

### 7. One best practice
Order routes from specific to general: place static paths (like `/users/me`) above dynamic path routes (like `/users/{user_id}`).

---

## 5. Query Parameters & Default Constraints

### 1. What it is
Key-value parameters appended to the URL query string after `?` (e.g., `/items?search=fastapi&limit=10`) that map to view function arguments not defined in the path pattern.

### 2. Why it exists
Allows client-side filtering, sorting, searching, and pagination of collection resources without cluttering the URL route path.

### 3. How it works
FastAPI parses arguments not present in the `{path}` pattern from the query string, applies default values or optional handling, and validates constraints using `Query()`.

### 4. Minimal example
```python
from typing import Optional
from fastapi import FastAPI, Query

app = FastAPI()

@app.get("/items/")
async def read_items(
    search: Optional[str] = Query(None, max_length=50),
    page: int = Query(1, ge=1),
    limit: int = Query(10, le=100)
):
    return {"search": search, "page": page, "limit": limit}
```

### 5. One exercise
**Exercise**: Create a GET endpoint `/search` that accepts an optional `q` string, `skip` (default 0), and `limit` (default 20, max 50).

### 6. One common mistake
Confusing path parameters and query parameters by omitting the variable from the `{path}` string pattern.

### 7. One best practice
Use `Query()` to set strict constraints (`min_length`, `max_length`, `ge`, `le`) to protect database queries from invalid client inputs.

---

## 6. Request Body & Pydantic Validation Models

### 1. What it is
Parsing, validating, and deserializing JSON payloads sent in HTTP POST/PUT/PATCH request bodies using **Pydantic** `BaseModel` classes.

### 2. Why it exists
Eliminates manual JSON payload extraction and validation checks. Pydantic guarantees that data arriving inside the view function strictly matches the declared schema types.

### 3. How it works
FastAPI reads the HTTP request stream, parses JSON bytes into a dict, passes it to the specified Pydantic `BaseModel` class for validation, and injects the instantiated model object into the view parameter.

### 4. Minimal example
```python
from typing import Optional
from fastapi import FastAPI
from pydantic import BaseModel, Field, EmailStr

app = FastAPI()

class UserCreate(BaseModel):
    username: str = Field(..., min_length=3, max_length=50)
    email: EmailStr
    age: Optional[int] = Field(None, ge=18, le=120)

@app.post("/users/")
async def create_user(user: UserCreate):
    return {"status": "created", "username": user.username, "email": user.email}
```

### 5. One exercise
**Exercise**: Define a Pydantic schema `Product` with `name` (str min length 2), `price` (float > 0), and optional `tags` (list of strings). Create a POST endpoint that accepts it.

### 6. One common mistake
Passing raw Python dicts without Pydantic schemas for complex request bodies, forfeiting automatic type validation and OpenAPI doc generation.

### 7. One best practice
Separate input validation schemas (e.g. `UserCreate`) from output response schemas (e.g. `UserResponse`).

---

## 7. Response Models & Data Filtering

### 1. What it is
Declaring the output data schema of an endpoint using `response_model=ModelClass` inside the path operation decorator.

### 2. Why it exists
Ensures returned responses strictly adhere to defined output schemas, automatically filtering out sensitive database fields (like `hashed_password`) and serializing ORM objects to JSON.

### 3. How it works
FastAPI takes the data returned by the view function, passes it through the declared `response_model`, applies field filtering (`response_model_exclude_unset=True`), serializes it to JSON, and updates the OpenAPI schema docs.

### 4. Minimal example
```python
from fastapi import FastAPI
from pydantic import BaseModel, EmailStr

app = FastAPI()

class UserIn(BaseModel):
    username: str
    password: str
    email: EmailStr

class UserOut(BaseModel):
    username: str
    email: EmailStr

@app.post("/users/", response_model=UserOut)
async def create_user(user: UserIn):
    # Password is automatically excluded from the final JSON response payload!
    return user
```

### 5. One exercise
**Exercise**: Create an endpoint that reads a full database user object containing `id`, `username`, `email`, and `password_hash`, but uses `response_model` to return only `id` and `username`.

### 6. One common mistake
Leaking sensitive database internal attributes in public API responses because no output `response_model` was declared.

### 7. One best practice
Use `response_model_exclude_unset=True` when returning partial payloads for PATCH endpoints to omit unset fields cleanly.

---

## 8. HTTP Status Codes & Response Customization

### 1. What it is
Configuring appropriate standard HTTP status codes (`201 Created`, `204 No Content`, `404 Not Found`) and returning custom response types (`JSONResponse`, `HTMLResponse`, `StreamingResponse`, `FileResponse`).

### 2. Why it exists
Communicates operation outcomes using standardized HTTP status codes and appropriate media content types.

### 3. How it works
FastAPI sets default response status codes via `status_code=status.HTTP_201_CREATED` on decorators or allows returning explicit Starlette `Response` objects directly.

### 4. Minimal example
```python
from fastapi import FastAPI, status, Response
from fastapi.responses import JSONResponse

app = FastAPI()

@app.post("/items/", status_code=status.HTTP_201_CREATED)
async def create_item(name: str):
    return {"name": name}

@app.delete("/items/{item_id}", status_code=status.HTTP_204_NO_CONTENT)
async def delete_item(item_id: int):
    return Response(status_code=status.HTTP_204_NO_CONTENT)
```

### 5. One exercise
**Exercise**: Write a DELETE route that returns `204 No Content` on success, and a POST route returning `201 Created` with custom headers.

### 6. One common mistake
Returning status code `200 OK` for error conditions or resource creation steps instead of using standard HTTP status codes (`201`, `400`, `404`).

### 7. One best practice
Import and use `fastapi.status` constants (e.g. `status.HTTP_201_CREATED`) instead of hardcoding raw integer numbers.

---

## 9. Dependency Injection System (`Depends`)

### 1. What it is
FastAPI's built-in **Dependency Injection** system, configured using `Depends(dependency_function)`, which handles shared logic, database sessions, authentication checks, and query parameters automatically.

### 2. Why it exists
Eliminates code duplication across routes, simplifies testing via dependency overrides, and manages resource lifetimes (like opening and closing database sessions automatically).

### 3. How it works
Before executing a view handler, FastAPI inspects its `Depends()` arguments, resolves and executes the dependency functions recursively, injects their returned values into the handler, and executes cleanup code (`yield`) after the response finishes.

### 4. Minimal example
```python
from typing import Optional
from fastapi import FastAPI, Depends

app = FastAPI()

# Shared Common Query Parameters Dependency
def common_parameters(q: Optional[str] = None, skip: int = 0, limit: int = 100):
    return {"q": q, "skip": skip, "limit": limit}

@app.get("/items/")
async def read_items(commons: dict = Depends(common_parameters)):
    return commons

@app.get("/users/")
async def read_users(commons: dict = Depends(common_parameters)):
    return commons
```

### 5. One exercise
**Exercise**: Write a dependency function `verify_token(x_token: str = Header(...))` that checks for a header `X-Token: secret`. Inject it into a protected endpoint.

### 6. One common mistake
Calling the dependency function manually inside the decorator (e.g., `Depends(common_parameters())` with parentheses) instead of passing the uncalled function reference (`Depends(common_parameters)`).

### 7. One best practice
Use `yield` inside dependency functions for context management (e.g., opening database connections before request execution and automatically closing them after).

---

## 10. Error Handling & `HTTPException`

### 1. What it is
Raising `HTTPException` or registering custom exception handlers via `@app.exception_handler` to return structured JSON error payloads to clients.

### 2. Why it exists
Provides clean error responses when business logic fails (e.g., item not found, unauthorized access, invalid state) without crashing the application server.

### 3. How it works
When `raise HTTPException(status_code, detail)` is executed, FastAPI interrupts request execution, catches the exception in Starlette middleware, and formats it into a standardized JSON response (`{"detail": "..."}`).

### 4. Minimal example
```python
from fastapi import FastAPI, HTTPException, status

app = FastAPI()

items = {"foo": "The Foo Item"}

@app.get("/items/{item_id}")
async def read_item(item_id: str):
    if item_id not in items:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail=f"Item '{item_id}' was not found in the system."
        )
    return {"item": items[item_id]}
```

### 5. One exercise
**Exercise**: Raise a `400 Bad Request` `HTTPException` if a user attempts to register with an email domain other than `@company.com`.

### 6. One common mistake
Returning a dict containing `{"error": "not found"}` instead of raising `HTTPException`, which fails to set the actual HTTP response status code to `404`.

### 7. One best practice
Pass a structured dict or list to `detail=` inside `HTTPException` for rich client validation error payloads.

---

## 11. Form Data & File Uploads (`UploadFile` & `File`)

### 1. What it is
Handling form submissions and file uploads using `Form()`, `File()` for raw bytes, and `UploadFile` for memory-efficient background file streaming.

### 2. Why it exists
`UploadFile` streams large files to disk/spool memory rather than loading the entire file payload into Python RAM at once, preventing memory exhaustion attacks.

### 3. How it works
FastAPI uses `python-multipart` to parse `multipart/form-data` request bodies. `UploadFile` exposes an async file-like interface with methods like `read()`, `write()`, and `seek()`.

### 4. Minimal example
```python
from fastapi import FastAPI, UploadFile, File, Form

app = FastAPI()

@app.post("/upload/")
async def upload_file(
    username: str = Form(...),
    file: UploadFile = File(...)
):
    contents = await file.read()
    return {
        "username": username,
        "filename": file.filename,
        "content_type": file.content_type,
        "size_bytes": len(contents)
    }
```

### 5. One exercise
**Exercise**: Write an endpoint `/upload-image/` that accepts an `UploadFile` and returns an error if the uploaded file's `content_type` is not `image/png` or `image/jpeg`.

### 6. One common mistake
Forgetting to install `python-multipart` (`pip install python-multipart`), which raises a runtime exception when file routes are called.

### 7. One best practice
Use `UploadFile` instead of `bytes = File(...)` for all production file endpoints to ensure low memory consumption.

---

## 12. Authentication & Security (JWT & OAuth2)

### 1. What it is
Implementing stateless API authentication using **OAuth2 Password Bearer flow** and **JSON Web Tokens (JWT)** via `OAuth2PasswordRequestForm` and `PyJWT`.

### 2. Why it exists
Secures sensitive API routes by validating client identity tokens without storing session state on the server.

### 3. How it works
The client posts credentials to a `/token` route, receiving a signed JWT. For protected routes, `OAuth2PasswordBearer` extracts the token from the `Authorization: Bearer <token>` header, decodes the signature, and injects the user object via dependency injection.

### 4. Minimal example
```python
from datetime import datetime, timedelta
from typing import Optional
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
import jwt

app = FastAPI()
SECRET_KEY = "super-secret-jwt-key"
ALGORITHM = "HS256"

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

def create_access_token(data: dict, expires_delta: Optional[timedelta] = None):
    to_encode = data.copy()
    expire = datetime.utcnow() + (expires_delta or timedelta(minutes=15))
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

@app.post("/token")
async def login(form_data: OAuth2PasswordRequestForm = Depends()):
    if form_data.username == "admin" and form_data.password == "secret":
        token = create_access_token(data={"sub": form_data.username})
        return {"access_token": token, "token_type": "bearer"}
    raise HTTPException(status_code=400, detail="Incorrect username or password")

@app.get("/users/me")
async def read_users_me(token: str = Depends(oauth2_scheme)):
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        return {"username": payload.get("sub")}
    except jwt.PyJWTError:
        raise HTTPException(status_code=401, detail="Invalid token")
```

### 5. One exercise
**Exercise**: Protect a route `/dashboard` by creating a `get_current_user` dependency that decodes the JWT and raises a `401 Unauthorized` if invalid or expired.

### 6. One common mistake
Hardcoding secret JWT signing keys directly in application source files instead of loading them from secure environment variables.

### 7. One best practice
Leverage FastAPI's interactive Swagger UI (`/docs`), which natively integrates with `OAuth2PasswordBearer` to provide an "Authorize" login button.

---

## 13. Database Integration (SQLAlchemy Async ORM / SQLModel)

### 1. What it is
Connecting FastAPI asynchronously to relational databases (PostgreSQL, SQLite) using **SQLAlchemy 2.0 Async Session** or **SQLModel**.

### 2. Why it exists
Enables non-blocking database queries over async database drivers (`asyncpg`, `aiosqlite`), preventing database I/O calls from bottlenecking FastAPI's event loop.

### 3. How it works
A database engine creates an async session factory. FastAPI's dependency system yields a `AsyncSession` per request, executing queries using `await session.execute()`, and automatically closes the session after the request finishes.

### 4. Minimal example
```python
from fastapi import FastAPI, Depends
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession, async_sessionmaker
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column
from sqlalchemy import select

DATABASE_URL = "sqlite+aiosqlite:///./test.db"

engine = create_async_engine(DATABASE_URL, echo=True)
async_session = async_sessionmaker(engine, expire_on_commit=False)

class Base(DeclarativeBase): pass

class User(Base):
    __tablename__ = "users"
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str]

app = FastAPI()

async def get_db():
    async with async_session() as session:
        yield session

@app.get("/users/")
async def list_users(db: AsyncSession = Depends(get_db)):
    result = await db.execute(select(User))
    return result.scalars().all()
```

### 5. One exercise
**Exercise**: Implement a POST `/users/` endpoint using the `get_db` dependency to insert a new `User` instance and commit it asynchronously.

### 6. One common mistake
Using synchronous database drivers (like standard `psycopg2` or `sqlite3`) inside `async def` routes, blocking the main event loop during database I/O operations.

### 7. One best practice
Always set `expire_on_commit=False` when instantiating `async_sessionmaker` to prevent implicit blocking I/O attribute access after commits.

---

## 14. Asynchronous Programming (`async` / `await`)

### 1. What it is
Python's native concurrency model (`asyncio`) allowing non-blocking I/O operations using `async def` and `await`.

### 2. Why it exists
Allows a single Python process thread to handle thousands of concurrent network connections by pausing execution during I/O waits (database, HTTP APIs) and switching to other pending tasks.

### 3. How it works
FastAPI runs `async def` view functions directly on the main ASGI event loop. When `await` is encountered on an async I/O call, control returns to the event loop to process other incoming requests until the awaited operation completes.

### 4. Minimal example
```python
import asyncio
import httpx
from fastapi import FastAPI

app = FastAPI()

@app.get("/fetch-external")
async def fetch_data():
    async with httpx.AsyncClient() as client:
        # Non-blocking async HTTP fetch call
        response = await client.get("https://httpbin.org/delay/1")
        return response.json()
```

### 5. One exercise
**Exercise**: Write an async route that uses `asyncio.gather()` to fetch data from two separate external URLs in parallel, returning the combined result.

### 6. One common mistake
Using blocking synchronous libraries (like `requests.get()` or `time.sleep()`) inside `async def` route functions. (Use `httpx` and `asyncio.sleep()` instead!).

### 7. One best practice
If you must use a blocking library (like `requests`), declare the route as a standard synchronous function `def route():` (without `async`), and FastAPI will automatically run it in a separate background threadpool worker!

---

## 15. Middleware & Request Lifecycle Hooks

### 1. What it is
Functions that sit between incoming HTTP requests and path handlers, intercepting and modifying requests before they arrive and responses before they depart.

### 2. Why it exists
Enables global cross-cutting functionality such as CORS header injection, request execution timing, custom logging, and global header processing.

### 3. How it works
Middleware wraps the ASGI application stack. Each middleware layer executes code before calling `call_next(request)`, inspects or modifies the response, and returns it up the middleware chain.

### 4. Minimal example
```python
import time
from fastapi import FastAPI, Request
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

# Built-in CORS Middleware
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["*"],
    allow_headers=["*"],
)

# Custom Process Time Middleware
@app.middleware("http")
async def add_process_time_header(request: Request, call_next):
    start_time = time.time()
    response = await call_next(request)
    process_time = time.time() - start_time
    response.headers["X-Process-Time"] = str(process_time)
    return response
```

### 5. One exercise
**Exercise**: Write a custom middleware that checks for a header `X-API-KEY` on all requests matching `/api/` and returns a `401 JSONResponse` if missing.

### 6. One common mistake
Performing heavy, slow database or I/O computations inside middleware functions for every request without caching.

### 7. One best practice
Place standard security middleware (like `CORSMiddleware` and `TrustedHostMiddleware`) at the top of your middleware stack initialization.

---

## 16. Background Tasks (`BackgroundTasks`)

### 1. What it is
FastAPI's built-in `BackgroundTasks` class, which allows scheduling lightweight tasks to run *after* returning an HTTP response to the client.

### 2. Why it exists
Improves user experience by executing non-critical tasks (sending confirmation emails, updating analytics, generating thumbnails) asynchronously after sending the HTTP response payload.

### 3. How it works
FastAPI captures task function references added via `background_tasks.add_task()`, sends the HTTP response immediately to the client, and executes the tasks sequentially on the ASGI event loop/threadpool.

### 4. Minimal example
```python
import time
from fastapi import FastAPI, BackgroundTasks

app = FastAPI()

def write_audit_log(message: str):
    time.sleep(2)  # Simulate file I/O or email sending
    with open("log.txt", "a") as f:
        f.write(f"{message}\n")

@app.post("/send-notification/{email}")
async def send_notification(email: str, background_tasks: BackgroundTasks):
    background_tasks.add_task(write_audit_log, f"Notification sent to {email}")
    return {"message": "Notification process scheduled"}
```

### 5. One exercise
**Exercise**: Create an endpoint `/register` that accepts a user email and uses `BackgroundTasks` to simulate sending a welcome email without slowing down the HTTP response.

### 6. One common mistake
Using `BackgroundTasks` for heavy, resource-intensive distributed queue operations (use Celery, Redis Queue, or RabbitMQ for heavy background jobs instead).

### 7. One best practice
Keep `BackgroundTasks` functions lightweight and idempotent; pass primitive data values (strings, ints) rather than complex open objects.

---

## 17. Modular Architecture (`APIRouter`)

### 1. What it is
Organizing related application routes into modular router files using `APIRouter` (similar to Flask Blueprints).

### 2. Why it exists
Prevents creating massive single-file applications by splitting endpoints into logical feature modules (`users.py`, `items.py`, `auth.py`).

### 3. How it works
`APIRouter` instances declare routes with their own prefixes (`prefix="/users"`) and tags (`tags=["Users"]`). The main `FastAPI` app includes them using `app.include_router(users_router)`.

### 4. Minimal example
```python
# routers/users.py
from fastapi import APIRouter

router = APIRouter(prefix="/users", tags=["Users"])

@router.get("/")
async def list_users():
    return [{"id": 1, "username": "alice"}]

# main.py
from fastapi import FastAPI
from routers.users import router as users_router

app = FastAPI()
app.include_router(users_router)
```

### 5. One exercise
**Exercise**: Create a `products_router` with `prefix="/products"` and register it in the main application file.

### 6. One common mistake
Forgetting to call `app.include_router(router)` in `main.py`, causing modular routes to return 404 errors.

### 7. One best practice
Use `tags=["ModuleName"]` on `APIRouter` to group routes visually into clean categories inside the Swagger UI documentation (`/docs`).

---

## 18. Automated Testing (`TestClient` & Pytest)

### 1. What it is
Testing FastAPI applications using Starlette's `TestClient` (built on `httpx`) alongside `pytest` for automated unit and integration tests.

### 2. Why it exists
Ensures application routes, input validations, security decorators, and database operations function correctly without needing to launch a live Uvicorn web server.

### 3. How it works
`TestClient(app)` simulates ASGI requests directly against the FastAPI app instance in-memory, returning `Response` objects that mirror live server responses.

### 4. Minimal example
```python
# main.py
from fastapi import FastAPI

app = FastAPI()

@app.get("/items/{item_id}")
async def read_item(item_id: int):
    return {"item_id": item_id}

# test_main.py
from fastapi.testclient import TestClient
from main import app

client = TestClient(app)

def test_read_item():
    response = client.get("/items/42")
    assert response.status_code == 200
    assert response.json() == {"item_id": 42}

def test_read_invalid_item():
    response = client.get("/items/invalid")
    assert response.status_code == 422  # Validation Error
```

### 5. One exercise
**Exercise**: Write a test for a POST endpoint using `TestClient` to verify both a successful `201 Created` payload and a `422` validation error payload.

### 6. One common mistake
Attempting to run asynchronous tests with `TestClient` without using `httpx.AsyncClient` or `pytest-asyncio`.

### 7. One best practice
Use `app.dependency_overrides[get_db] = override_get_db` to replace production database dependencies with an in-memory SQLite test database during automated test runs.

---

## 19. Production Deployment (Docker, Gunicorn + Uvicorn Workers, Nginx)

### 1. What it is
Deploying FastAPI applications in production using containerized **Docker** images, managed by **Gunicorn process manager** running multiple **Uvicorn worker processes** (`uvicorn.workers.UvicornWorker`), behind an **Nginx** reverse proxy.

### 2. Why it exists
A single Uvicorn instance runs on a single CPU core. Production deployments require process supervision, multiple CPU worker processes, TLS termination, static asset serving, and container isolation.

### 3. How it works
Nginx receives HTTPS traffic from the web, terminates SSL, and proxies requests to Gunicorn. Gunicorn supervises multiple worker processes, distributing requests across Uvicorn ASGI workers running on multiple CPU cores.

### 4. Minimal example
```dockerfile
# Dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

# Run Gunicorn with Uvicorn worker class across 4 CPU workers
CMD ["gunicorn", "-w", "4", "-k", "uvicorn.workers.UvicornWorker", "main:app", "--bind", "0.0.0.0:8000"]
```

### 5. One exercise
**Exercise**: Write a `Dockerfile` for a FastAPI project, build the image using `docker build -t fastapi-app .`, and run the container on port `8000`.

### 6. One common mistake
Running Uvicorn with `--reload` in production environments, which wastes CPU memory and poses security risks.

### 7. One best practice
Use official multi-stage Docker builds or lean Python base images (`python:3.11-slim`) to keep production container image sizes minimal.

---

## 💡 A Tip for Both Flask and FastAPI

> [!TIP]
> **Mastery Rule for Web Developers**:
> Don't just memorize syntax. When building any route or backend system, evaluate:
> 1. **Data Contract**: Are inputs strictly validated via Pydantic (FastAPI) or Schemas (Flask)?
> 2. **Concurrency**: Is the operation I/O bound (`async def` with `await`) or CPU bound (multiprocessing)?
> 3. **Security**: Is every protected endpoint guarded by token validation dependencies (`Depends(oauth2_scheme)`)?
