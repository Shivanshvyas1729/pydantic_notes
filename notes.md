1️⃣ 1_pydantic_why.py

👉 Goal: Show how Field, Annotated, and built-in validators work

from pydantic import BaseModel, EmailStr, AnyUrl, Field
from typing import List, Dict, Optional, Annotated

🔹 Why these imports?

BaseModel → parent class for all Pydantic models

EmailStr → validates email format (needs email-validator)

AnyUrl → validates any URL (http, https, ftp, etc.)

Field → add constraints + metadata

Annotated → modern (v2) way to attach Field to a type

class Patient(BaseModel):


👉 This class defines the schema + validation rules

    name: Annotated[
        str,
        Field(
            max_length=50,                     # max allowed characters
            title='Name of the patient',       # used in OpenAPI docs
            description='Give the name of the patient in less than 50 chars',
            examples=['Nitish', 'Amit']        # Swagger UI example values
        )
    ]


📌 Other useful Field options

min_length

regex

alias

deprecated=True

    email: EmailStr           # validates email format
    linkedin_url: AnyUrl      # validates URL

    age: int = Field(gt=0, lt=120)


📌 Numeric constraints

gt → greater than

ge → greater or equal

lt → less than

le → less or equal

multiple_of

    weight: Annotated[float, Field(gt=0, strict=True)]


📌 strict=True

❌ "75.2" → rejected

✅ 75.2 → accepted
(useful when you don’t want type coercion)

    married: Annotated[
        bool,
        Field(default=None, description='Is the patient married or not')
    ]


📌 Optional with metadata

    allergies: Annotated[
        Optional[List[str]],
        Field(default=None, max_length=5)
    ]


📌 max_length=5 → max number of items in list

    contact_details: Dict[str, str]


📌 Any string → string mapping (phone, email, emergency, etc.)

patient_info = {
    'name':'nitish',
    'email':'abc@gmail.com',
    'linkedin_url':'http://linkedin.com/1322',
    'age': '30',           # STRING → auto converted to int
    'weight': 75.2,
    'contact_details':{'phone':'2353462'}
}


📌 Pydantic does type coercion by default

2️⃣ _field_validator.py

👉 Goal: Custom validation for individual fields

from pydantic import BaseModel, EmailStr, field_validator

class Patient(BaseModel):

    @field_validator('email')
    @classmethod
    def email_validator(cls, value):


📌 Runs only for email field

        valid_domains = ['hdfc.com', 'icici.com']
        domain_name = value.split('@')[-1]

        if domain_name not in valid_domains:
            raise ValueError('Not a valid domain')


✔ Enforces business rule, not format rule

    @field_validator('name')
    @classmethod
    def transform_name(cls, value):
        return value.upper()


📌 Used for data transformation

    @field_validator('age', mode='after')


📌 mode='after'

Runs after type coercion

'30' → 30 → then validated

Other mode:

mode='before' → raw input

3️⃣ model_validator.py

👉 Goal: Validate using multiple fields together

from pydantic import BaseModel, EmailStr, model_validator

    @model_validator(mode='after')
    def validate_emergency_contact(cls, model):


📌 Used when:

Validation depends on more than one field

        if model.age > 60 and 'emergency' not in model.contact_details:
            raise ValueError(
                'Patients older than 60 must have an emergency contact'
            )


📌 Field validators ❌
📌 Model validator ✅

4️⃣ computed_fields.py

👉 Goal: Derived values (not stored, auto calculated)

from pydantic import BaseModel, EmailStr, computed_field

    @computed_field
    @property
    def bmi(self) -> float:


📌 Why computed field?

Not part of input

Automatically included in output

Recalculated every time

        bmi = round(self.weight / (self.height ** 2), 2)
        return bmi


📌 Appears in:

model_dump()

API responses

5️⃣ nested_models.py

👉 Goal: Structured & reusable models

class Address(BaseModel):
    city: str
    state: str
    pin: str

class Patient(BaseModel):
    name: str
    gender: str
    age: int
    address: Address


📌 Benefits:

Clean structure

Automatic validation

Reusable Address model

patient1 = Patient(**patient_dict)


📌 Pydantic auto-validates nested models
(no extra code needed)

6️⃣ Serialization (model_dump)
temp = patient1.model_dump(exclude_unset=True)


📌 Common options:

exclude_unset=True → skip default values

exclude_none=True

include={'name', 'age'}

exclude={'address'}

🔑 Big picture (remember this)
Feature	When to use
Field	Simple constraints & metadata
Annotated	Clean modern syntax (v2)
field_validator	Single field logic
model_validator	Cross-field logic
computed_field	Derived values
Nested models	Complex structured data
