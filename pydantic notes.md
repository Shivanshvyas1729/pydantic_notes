# 📘 Pydantic v2 Notes (Complete Cheat Sheet)

Pydantic is a Python library for **data validation, parsing, and serialization** using Python type hints.

It automatically:
- ✅ Validates input data
- ✅ Converts compatible types (type coercion)
- ✅ Raises clear validation errors
- ✅ Generates schemas (FastAPI/OpenAPI)

---

<details><summary>ANNOTATED fn </summary>
`Annotated` comes from Python's `typing` module. It lets you **attach extra metadata to a type** without changing the type itself.

```python
from typing import Annotated
```

Think of it as:

> **Type + Additional Information**

The additional information is mainly used by libraries (like FastAPI, Pydantic, LangGraph, etc.), not by Python itself.

---

## Syntax

```python
Annotated[actual_type, metadata]
```

For example:

```python
age: Annotated[int, "Age in years"]
```

Here:

* Actual type = `int`
* Metadata = `"Age in years"`

Python still treats `age` as an `int`.

---

## Example 1

Without `Annotated`:

```python
name: str
```

With `Annotated`:

```python
name: Annotated[str, "User's full name"]
```

The type is still **`str`**.

The extra `"User's full name"` is metadata.

---

## Example 2 (Pydantic)

```python
from typing import Annotated
from pydantic import BaseModel, Field

class User(BaseModel):
    age: Annotated[int, Field(gt=0)]
```

Here:

* Type = `int`
* Metadata = `Field(gt=0)`

Pydantic reads the metadata and validates:

```python
User(age=25)
```

✅ Works

```python
User(age=-5)
```

❌ Raises a validation error because `gt=0` means "greater than 0".

---

## Example 3 (FastAPI)

```python
from typing import Annotated
from fastapi import Query

def search(
    limit: Annotated[int, Query(gt=0, le=100)]
):
    ...
```

Here:

* Type = `int`
* Metadata = `Query(gt=0, le=100)`

FastAPI uses the metadata to validate the query parameter.

---

## Example 4 (LangGraph)

LangGraph uses `Annotated` to specify how state should be updated.

```python
from typing import Annotated

messages: Annotated[list, add_messages]
```

Here:

* Type = `list`
* Metadata = `add_messages`

LangGraph understands:

> "When updating this list, use the `add_messages` function instead of replacing the entire list."

Without `Annotated`, LangGraph wouldn't know that behavior.

---

## Why not just use `int`?

You could write:

```python
age: int
```

But then there's nowhere to attach additional information like:

* validation rules
* descriptions
* merge strategies
* documentation
* dependency injection

`Annotated` solves that.

---

## General form

```python
Annotated[
    Type,
    Metadata1,
    Metadata2,
    Metadata3
]
```

You can even have multiple pieces of metadata:

```python
Annotated[
    int,
    "Age",
    "Must be positive"
]
```

The actual type is still `int`.

---

## Simple analogy

Imagine a parcel.

```
Parcel
┌───────────────────────────┐
│ Item: Laptop              │  ← Actual type
│ Fragile                   │  ← Metadata
│ Handle with care          │  ← Metadata
└───────────────────────────┘
```

The item is still a **Laptop**.

The stickers don't change what's inside—they provide extra information.

Similarly,

```python
Annotated[int, Field(gt=0)]
```

means:

* **Type:** `int`
* **Metadata:** `gt=0`

The value is still an integer, but libraries can use the metadata to add special behavior.

### In your import

```python
from typing import (
    List,
    Dict,
    Optional,
    Annotated
)
```

you're simply importing `Annotated` so you can later write type hints like:

```python
from pydantic import Field

age: Annotated[int, Field(gt=0)]
```

or

```python
messages: Annotated[list, add_messages]
```

where the first part (`int`, `list`) is the actual type and the second part provides extra instructions for a library that understands it.
</details>
# 1. BaseModel

Every Pydantic model inherits from `BaseModel`.

```python
from pydantic import BaseModel

class Patient(BaseModel):
    name: str
    age: int
```

Creating an object:

```python
patient = Patient(
    name="Nitish",
    age="30"
)
```

Pydantic automatically converts:

```
"30"  →  30
```

This behavior is called **type coercion**.

---

# 2. Common Imports

```python
from pydantic import (
    BaseModel,
    Field,
    EmailStr,
    AnyUrl,
    field_validator,
    model_validator,
    computed_field
)

from typing import (
    List,
    Dict,
    Optional,
    Annotated
)
```

## What each import does

| Import | Purpose |
|---------|----------|
| `BaseModel` | Base class for all models |
| `Field` | Add validation rules and metadata |
| `Annotated` | Modern (v2) way to attach `Field` to a type |
| `EmailStr` | Validates email addresses |
| `AnyUrl` | Validates URLs |
| `field_validator` | Validate a single field |
| `model_validator` | Validate multiple fields together |
| `computed_field` | Create calculated fields |

---

# 3. Field()

`Field()` is used to add:

- Validation constraints
- Default values
- Documentation metadata
- Examples

Example:

```python
name: Annotated[
    str,
    Field(
        max_length=50,
        title="Patient Name",
        description="Patient name under 50 characters",
        examples=["Nitish", "Amit"]
    )
]
```

---

## Common Field Parameters

### String Constraints

```python
Field(
    min_length=3,
    max_length=30,
    pattern=r"^[A-Za-z]+$"
)
```

| Option | Description |
|---------|-------------|
| `min_length` | Minimum characters |
| `max_length` | Maximum characters |
| `pattern` | Regular expression |
| `alias` | Alternate input name |
| `deprecated=True` | Marks field deprecated |

---

### Numeric Constraints

```python
age: int = Field(
    gt=0,
    lt=120
)
```

| Constraint | Meaning |
|------------|---------|
| `gt` | Greater than |
| `ge` | Greater than or equal |
| `lt` | Less than |
| `le` | Less than or equal |
| `multiple_of` | Must be divisible by value |

Example

```python
price: float = Field(gt=0)
```

---

### Strict Validation

Normally Pydantic converts types automatically.

```python
weight: float
```

Input:

```python
"75.2"
```

becomes

```python
75.2
```

If you don't want automatic conversion:

```python
weight: Annotated[
    float,
    Field(strict=True)
]
```

Now

```
❌ "75.2"
```

is rejected.

Only

```
✅ 75.2
```

is accepted.

---

# 4. Special Data Types

## Email Validation

```python
email: EmailStr
```

Accepts:

```
abc@gmail.com
```

Rejects:

```
abcgmail.com
```

---

## URL Validation

```python
linkedin: AnyUrl
```

Valid:

```
https://linkedin.com/in/nitish
```

Invalid:

```
linkedin.com
```

---

# 5. Optional Fields

```python
married: Optional[bool] = None
```

or

```python
married: Annotated[
    Optional[bool],
    Field(
        default=None,
        description="Is patient married?"
    )
]
```

---

# 6. Lists

```python
allergies: List[str]
```

Optional:

```python
Optional[List[str]]
```

Maximum number of items:

```python
allergies: Annotated[
    Optional[List[str]],
    Field(max_length=5)
]
```

---

# 7. Dictionaries

```python
contact_details: Dict[str, str]
```

Example

```python
{
    "phone":"9876543210",
    "emergency":"9988776655"
}
```

Useful for dynamic key-value data.

---

# 8. Type Coercion

By default Pydantic converts compatible values.

Example

Input

```python
{
    "age":"30",
    "weight":"75.2"
}
```

Output

```python
age = 30
weight = 75.2
```

Disable using

```python
strict=True
```

---

# 9. Field Validators

Use when validation involves **one field only**.

```python
from pydantic import field_validator
```

Example

```python
@field_validator("email")
@classmethod
def validate_email(cls, value):

    domain = value.split("@")[-1]

    if domain not in ["hdfc.com", "icici.com"]:
        raise ValueError("Invalid domain")

    return value
```

This validates business rules, **not email format**.

---

## Data Transformation

Validators can also modify data.

```python
@field_validator("name")
@classmethod
def uppercase_name(cls, value):
    return value.upper()
```

Input

```
nitish
```

Output

```
NITISH
```

---

## Validator Modes

### Before

```python
mode="before"
```

Runs before type conversion.

```
"30"
```

is still a string.

---

### After

```python
mode="after"
```

Runs after conversion.

```
"30"
```

becomes

```
30
```

before validation.

---

# 10. Model Validators

Use when validation depends on **multiple fields**.

```python
from pydantic import model_validator
```

Example

```python
@model_validator(mode="after")
def validate_patient(self):

    if (
        self.age > 60
        and "emergency" not in self.contact_details
    ):
        raise ValueError(
            "Emergency contact required."
        )

    return self
```

---

## Difference

| Validator | Used For |
|------------|----------|
| `field_validator` | One field |
| `model_validator` | Multiple fields |

---

# 11. Computed Fields

Sometimes a value shouldn't be stored.

It should be calculated automatically.

Example: BMI

```python
from pydantic import computed_field

@computed_field
@property
def bmi(self):

    return round(
        self.weight / (self.height ** 2),
        2
    )
```

Advantages

- Not part of input
- Automatically calculated
- Included in output
- Always up to date

Example

```python
patient.model_dump()
```

Output

```python
{
    "weight":70,
    "height":1.75,
    "bmi":22.86
}
```

---

# 12. Nested Models

Models can contain other models.

```python
class Address(BaseModel):

    city: str
    state: str
    pin: str


class Patient(BaseModel):

    name: str
    age: int

    address: Address
```

Input

```python
{
    "name":"Nitish",
    "age":24,

    "address":{
        "city":"Delhi",
        "state":"Delhi",
        "pin":"110001"
    }
}
```

Pydantic automatically validates nested objects.

No extra code required.

---

# 13. Serialization

Convert model → dictionary

```python
patient.model_dump()
```

---

## Common Options

Exclude fields with default values

```python
patient.model_dump(
    exclude_unset=True
)
```

Exclude `None`

```python
patient.model_dump(
    exclude_none=True
)
```

Include only specific fields

```python
patient.model_dump(
    include={"name","age"}
)
```

Exclude fields

```python
patient.model_dump(
    exclude={"address"}
)
```

---

# 14. Complete Example

```python
patient_info = {

    "name":"Nitish",

    "email":"abc@gmail.com",

    "linkedin_url":"https://linkedin.com",

    "age":"30",

    "weight":75.2,

    "contact_details":{
        "phone":"9876543210"
    }
}

patient = Patient(**patient_info)
```

Pydantic will:

- Validate email
- Validate URL
- Convert `"30"` → `30`
- Validate weight
- Validate nested models
- Run field validators
- Run model validators

---

# 📝 Quick Revision Table

| Feature | Purpose | Use When |
|----------|----------|----------|
| `BaseModel` | Define models | Every Pydantic model |
| `Field` | Constraints & metadata | Validation rules |
| `Annotated` | Modern way to attach `Field` | Pydantic v2 |
| `EmailStr` | Email validation | Email fields |
| `AnyUrl` | URL validation | Website links |
| `strict=True` | Disable type coercion | Exact data types required |
| `field_validator` | Validate one field | Email, name, age, etc. |
| `model_validator` | Validate multiple fields | Cross-field business logic |
| `computed_field` | Calculated property | BMI, totals, taxes |
| Nested Models | Reusable structured data | Address, Company, User |
| `model_dump()` | Serialize model | Convert model → dictionary |

---

# 🎯 Rule of Thumb

| Situation | Use |
|-----------|-----|
| Simple validation | `Field()` |
| String/number constraints | `Field()` |
| Add metadata | `Field()` |
| Validate one field | `field_validator` |
| Validate multiple fields together | `model_validator` |
| Calculate derived values | `computed_field` |
| Reuse complex objects | Nested Models |
| Convert model to dictionary | `model_dump()` |
