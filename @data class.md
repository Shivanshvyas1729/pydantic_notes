`@dataclass` is a decorator from Python's **`dataclasses`** module that automatically generates common methods like:

* `__init__()` (constructor)
* `__repr__()` (string representation)
* `__eq__()` (comparison)
* and others

It is mainly used for classes that **only store data**.

---

# Without `@dataclass`

Suppose you have this class:

```python
class Query:
    def __init__(self, text, filters=None):
        self.text = text
        self.filters = filters
```

To create an object:

```python
q = Query("What is AI?", {"doc_id": "123"})
```

You had to manually write the constructor.

---

# With `@dataclass`

```python
from dataclasses import dataclass

@dataclass
class Query:
    text: str
    filters: dict = None
```

That's it!

Python automatically creates this behind the scenes:

```python
class Query:
    def __init__(self, text, filters=None):
        self.text = text
        self.filters = filters
```

Now you can do:

```python
q = Query("What is AI?", {"doc_id": "123"})
```

without writing `__init__()` yourself.

---

# In Your Code

```python
@dataclass
class Embedding:
    doc_id: str
    vector: List[float]
    metadata: Dict[str, Any]
```

Python automatically creates something similar to:

```python
class Embedding:
    def __init__(self, doc_id, vector, metadata):
        self.doc_id = doc_id
        self.vector = vector
        self.metadata = metadata
```

So you can directly create an object:

```python
embedding = Embedding(
    doc_id="doc123",
    vector=[0.12, 0.45, 0.89],
    metadata={"chunk": 1}
)
```

---

# Another Example

Without `@dataclass`

```python
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age
```

With `@dataclass`

```python
from dataclasses import dataclass

@dataclass
class Student:
    name: str
    age: int
```

Both behave the same:

```python
s = Student("Alice", 20)

print(s.name)
print(s.age)
```

Output

```
Alice
20
```

---

# `__repr__()` Is Also Generated

Normally, printing an object gives something like:

```python
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age

s = Student("Alice", 20)

print(s)
```

Output:

```
<__main__.Student object at 0x7f3b...>
```

With `@dataclass`:

```python
from dataclasses import dataclass

@dataclass
class Student:
    name: str
    age: int

s = Student("Alice", 20)

print(s)
```

Output:

```
Student(name='Alice', age=20)
```

This makes debugging much easier.

---

# `__eq__()` Is Automatically Generated

Without `@dataclass`:

```python
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age

s1 = Student("Alice", 20)
s2 = Student("Alice", 20)

print(s1 == s2)
```

Output:

```
False
```

because they are two different objects in memory.

With `@dataclass`:

```python
from dataclasses import dataclass

@dataclass
class Student:
    name: str
    age: int

s1 = Student("Alice", 20)
s2 = Student("Alice", 20)

print(s1 == s2)
```

Output:

```
True
```

The comparison is based on the values of the fields, not object identity.

---

# Fields with Default Values

```python
from dataclasses import dataclass

@dataclass
class Query:
    text: str
    filters: dict = None
```

Now:

```python
q1 = Query("Explain AI")
q2 = Query("Explain AI", {"doc_id": "123"})
```

Both are valid because `filters` has a default value.

---

# Why Use `@dataclass`?

It reduces boilerplate for classes that primarily hold data.

Instead of writing:

```python
class Embedding:
    def __init__(self, doc_id, vector, metadata):
        self.doc_id = doc_id
        self.vector = vector
        self.metadata = metadata
```

you can simply write:

```python
@dataclass
class Embedding:
    doc_id: str
    vector: List[float]
    metadata: Dict[str, Any]
```

---

# Summary

| Feature      | Without `@dataclass`         | With `@dataclass`              |
| ------------ | ---------------------------- | ------------------------------ |
| `__init__()` | Write manually               | Generated automatically        |
| `__repr__()` | Default object address       | Readable output                |
| `__eq__()`   | Compares object identity     | Compares field values          |
| Boilerplate  | More                         | Less                           |
| Best Use     | Classes with custom behavior | Classes that mainly store data |

**In your RAG project**, `Embedding`, `Query`, `RetrievalResult`, and `Explanation` are simple data containers. Using `@dataclass` makes the code shorter, cleaner, and easier to maintain without changing how you create or use these objects.
