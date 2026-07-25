That's a great question. Think of **`raise`** and **`except ... as e`** as doing two completely different jobs.

* **`raise` = Throw an error.**
* **`except ... as e` = Catch an error that was thrown.**

## 1. Use `raise` when **your code detects something is wrong**

You are the one creating the exception.

Example:

```python
def withdraw(balance, amount):
    if amount > balance:
        raise ValueError("Insufficient balance")

    return balance - amount
```

Here, **you** know the operation is invalid, so **you raise** an exception.

---

## 2. Use `except ... as e` when **another function might fail**

Example:

```python
try:
    with open("config.json") as f:
        data = f.read()
except FileNotFoundError as e:
    print("Config file missing")
```

Here,

* `open()` raises `FileNotFoundError`
* You catch it using `except`.

You didn't create the error—you handled it.

---

# Engineering decision tree

### Case 1: I know something is invalid

Use **`raise`**

```python
if age < 0:
    raise ValueError("Age cannot be negative")
```

---

### Case 2: Another library/function may fail

Use **`try` + `except`**

```python
try:
    image = Image.open(path)
except FileNotFoundError:
    ...
```

---

### Case 3: I want to add more information

Use **`except ... as e`** + **`raise ... from e`**

```python
try:
    model = torch.load(path)
except Exception as e:
    raise ModelLoadError(
        f"Couldn't load model: {path}"
    ) from e
```

---

### Case 4: I can recover

Use **`except`**

```python
try:
    config = load_config()
except FileNotFoundError:
    config = DEFAULT_CONFIG
```

No need to raise anything because you've fixed the problem.

---

### Case 5: I cannot recover

Catch it only to add context, then raise again.

```python
try:
    parse_file(path)
except Exception as e:
    raise RuntimeError("Parsing failed") from e
```

---

# A real AI engineering example

```python
def load_model(path):
    try:
        return torch.load(path)
    except FileNotFoundError as e:
        raise ModelLoadError(
            f"Model '{path}' not found"
        ) from e
```

Who raised `FileNotFoundError`?

> `torch.load()`

Who caught it?

> `except FileNotFoundError as e`

Who created `ModelLoadError`?

> `raise ModelLoadError(...)`

---

# Simple rule you'll use every day

✅ Use **`raise`** when **you discover** an error.

```python
if user is None:
    raise UserNotFoundError()
```

---

✅ Use **`except ... as e`** when **someone else may raise** an error.

```python
try:
    connect_to_database()
except ConnectionError as e:
    ...
```

---

## The question every engineer asks

Before writing `except`, ask yourself:

> **Can I do something useful with this error?**

* **Yes** → Catch it (`except`), recover or add context.
* **No** → Don't catch it. Let it propagate to the caller.

This is why senior engineers avoid wrapping every function in `try/except`. They only catch exceptions where they can meaningfully handle them or translate them into a more appropriate error for the next layer.
