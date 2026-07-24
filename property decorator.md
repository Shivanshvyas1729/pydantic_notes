# Python `@property` Decorator

The `@property` decorator in Python allows you to define methods that behave like attributes. It provides a clean and Pythonic way to implement **getters**, **setters**, and **deleters** while keeping the public API simple.

Instead of calling a method like:

```python
obj.get_value()
```

you can simply write:

```python
obj.value
```

---

# Basic Example

```python
class Circle:
    def __init__(self, radius):
        self.radius = radius

    @property
    def area(self):
        return 3.14 * (self.radius ** 2)


my_circle = Circle(5)

print(my_circle.area)   # 78.5
```

### Output

```
78.5
```

Notice that `area` is accessed like an attribute (`my_circle.area`) instead of a method (`my_circle.area()`).

---

# How `@property` Works

A property can have three parts:

| Decorator | Purpose |
|-----------|---------|
| `@property` | Getter (read value) |
| `@property_name.setter` | Setter (assign/validate value) |
| `@property_name.deleter` | Deleter (delete or clean up value) |

---

# Complete Example

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age    # Calls the setter

    # Getter
    @property
    def age(self):
        return self._age

    # Setter
    @age.setter
    def age(self, value):
        if not isinstance(value, int):
            raise TypeError("Age must be an integer.")
        if value < 0:
            raise ValueError("Age cannot be negative.")
        self._age = value

    # Deleter
    @age.deleter
    def age(self):
        print("Deleting age...")
        del self._age
```

### Usage

```python
p = Person("Alice", 25)

# Reading
print(p.age)

# Updating
p.age = 30
print(p.age)

# Invalid update
try:
    p.age = -5
except ValueError as e:
    print(e)

# Delete
del p.age
```

### Output

```
25
30
Age cannot be negative.
Deleting age...
```

---

# Why Use `@property`?

The `@property` decorator is commonly used for:

- Data validation
- Read-only attributes
- Computed attributes
- Backward compatibility

---

# 1. Data Validation

A setter can validate incoming values before storing them.

```python
class Account:
    def __init__(self, balance):
        self.balance = balance

    @property
    def balance(self):
        return self._balance

    @balance.setter
    def balance(self, value):
        if value < 0:
            raise ValueError("Balance cannot be negative!")
        self._balance = value
```

### Usage

```python
acc = Account(100)

acc.balance = 200
print(acc.balance)

acc.balance = -50
```

### Output

```
200
ValueError: Balance cannot be negative!
```

---

# 2. Read-Only Attributes

Simply omit the setter to make an attribute read-only.

```python
class User:
    def __init__(self, user_id):
        self._user_id = user_id

    @property
    def user_id(self):
        return self._user_id
```

### Usage

```python
user = User("USR-9921")

print(user.user_id)

user.user_id = "NEW-ID"
```

### Output

```
USR-9921
AttributeError: can't set attribute
```

---

# 3. Computed Attributes

Properties are perfect for values that should be calculated dynamically.

```python
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height

    @property
    def area(self):
        return self.width * self.height
```

### Usage

```python
rect = Rectangle(4, 5)

print(rect.area)

rect.width = 10

print(rect.area)
```

### Output

```
20
50
```

Notice that the area updates automatically whenever the width changes.

---

# 4. Backward Compatibility

Suppose your original class exposed a public attribute.

### Before

```python
class Product:
    def __init__(self, price):
        self.price = price
```

Later, you decide to add validation without changing how users interact with the class.

### After

```python
class Product:
    def __init__(self, price):
        self.price = price

    @property
    def price(self):
        return self._price

    @price.setter
    def price(self, value):
        self._price = max(0, value)
```

External code continues to work exactly the same:

```python
p = Product(20)

p.price = 50
print(p.price)
```

No changes are required in existing code.

---

# Best Practices

- Use a leading underscore (`_value`) for the internal storage variable.
- Perform validation inside setters.
- Use properties instead of trivial `get_` and `set_` methods.
- Use read-only properties for values that should not be modified.
- Use properties for computed values instead of storing duplicate data.

---

# Summary

| Feature | Benefit |
|---------|---------|
| `@property` | Access methods like attributes |
| Getter | Returns a value |
| Setter | Validates or modifies assigned values |
| Deleter | Cleans up or deletes an attribute |
| Computed Property | Calculates values dynamically |
| Read-Only Property | Prevents modification |
| Validation | Ensures only valid data is stored |
| Backward Compatibility | Add logic without changing the public API |

---

# Key Takeaways

- `@property` makes methods behave like attributes.
- Setters allow validation before assigning values.
- Properties can be read-only by omitting the setter.
- Computed properties always return up-to-date values.
- They help maintain a clean, Pythonic, and maintainable API.

---

## References

- Python Official Documentation: https://docs.python.org/3/library/functions.html#property
- Python Data Model: https://docs.python.org/3/reference/datamodel.html
- Python Tutorial (Classes): https://docs.python.org/3/tutorial/classes.html
