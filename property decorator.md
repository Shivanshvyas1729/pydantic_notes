class Circle:
    def __init__(self, radius):
        self.radius = radius

    @property
    def area(self):
        return 3.14 * (self.radius ** 2)

my_circle = Circle(5)

print(my_circle.area) # Accessed as an attribute, output is 78.5


The @property decorator in Python is a built-in feature that allows you to define methods in a class that can be accessed and manipulated exactly like normal attributes. It provides a clean, Pythonic way to implement getters, setters, and deleters without breaking a class's external API. [1, 2, 3, 4, 5] 
## Core Syntax and Components
The @property ecosystem relies on three specific decorators applied to methods sharing the exact same name: [1, 6] 

* @property: Defines the getter method to read an attribute.
* @<property_name>.setter: Defines the setter method to assign or validate a value.
* @<property_name>.deleter: Defines the deleter method to clean up or delete an attribute. [6, 7] 

## Complete Implementation Example
The following example demonstrates data validation using a private internal attribute convention (_age). [7, 8] 

class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age  # This triggers the setter method automatically

    # 1. The Getter
    @property
    def age(self):
        """Gets the person's age."""
        return self._age

    # 2. The Setter (with validation logic)
    @age.setter
    def age(self, value):
        if not isinstance(value, int):
            raise TypeError("Age must be an integer.")
        if value < 0:
            raise ValueError("Age cannot be negative.")
        self._age = value

    # 3. The Deleter
    @age.deleter
    def age(self):
        print("Deleting age attribute...")
        del self._age
# --- Usage ---p = Person("Alice", 25)
# Reading the property (No parentheses needed!)
print(p.age)  # Output: 25
# Modifying the property (Triggers validation)
p.age = 30 
print(p.age)  # Output: 30
# Invalid modification attempttry:
    p.age = -5  # Raises ValueErrorexcept ValueError as e:
    print(e)  # Output: Age cannot be negative.
# Deleting the propertydel p.age

## Key Use Cases

* Data Validation: Sanitize or enforce rules on incoming data before saving it to your object.
* Read-Only Attributes: Omit the .setter method entirely to expose data that cannot be altered from outside the class.
* Computed Attributes: Calculate values dynamically on the fly (e.g., creating an area property from width and height) instead of storing stale data.
* Backward Compatibility: Safely replace a standard attribute with code logic later on without changing how external developers call your object. [4, 5, 8, 9, 10, 11, 12] 
The @property decorator manages internal object data using standard dot notation (obj.attribute). This technique provides clean code with full structural control behind the scenes. [1, 2, 3, 4, 5] 
------------------------------
## 1. Data Validation
Data validation prevents your object from accepting bad data. The setter method acts as a security guard, scanning incoming values before saving them. [6, 7, 8, 9, 10] 

class Account:
    def __init__(self, balance):
        self.balance = balance

    @property
    def balance(self):
        return self._balance

    @balance.setter
    def balance(self, value):
        # Enforce validation rules
        if value < 0:
            raise ValueError("Balance cannot be negative!")
        self._balance = value
acc = Account(100)
acc.balance = -50  # Throws ValueError: Balance cannot be negative!

## 2. Read-Only Attributes
Read-only attributes protect data that must never change after creation. By defining a getter with @property and omitting the .setter method, Python prevents external modifications. [7, 11] 

class User:
    def __init__(self, user_id):
        self._user_id = user_id

    @property
    def user_id(self):  # Getter only
        return self._user_id
user = User("USR-9921")
print(user.user_id)  # Works fine: "USR-9921"
user.user_id = "NEW-ID"  # Throws AttributeError: can't set attribute

## 3. Computed Attributes
Computed attributes calculate values live when requested instead of saving them as static fields. This practice eliminates duplicate data states and ensures calculations remain accurate. [6, 7, 12, 13] 

class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height

    @property
    def area(self):  # Calculated on the fly
        return self.width * self.height
rect = Rectangle(4, 5)
print(rect.area)  # Output: 20
rect.width = 10
print(rect.area)  # Output: 50 (Automatically updates!)

## 4. Backward Compatibility
Backward compatibility updates your internal class design without breaking existing dependencies. You can convert an unprotected public attribute into a managed property without forcing other developers to rewrite their code from obj.value to obj.get_value(). [14, 15, 16] 

# Phase 1: Original implementation used a basic attributeclass Product:
    def __init__(self, price):
        self.price = price  # Public, unsecured attribute
# Phase 2: Refactored later to include validation without breaking external scriptsclass Product:
    def __init__(self, price):
        self.price = price

    @property
    def price(self):
        return self._price

    @price.setter
    def price(self, value):
        self._price = max(0, value)  # Ensures price never drops below 0
# External code like 'p.price = 20' continues working identically across both setups!
