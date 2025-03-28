# Immutable DTOs in Python

This example demonstrates how to create an immutable Data Transfer Object (DTO) in Python using `dataclasses` and how to define an `Enum` for status values.

## Enum in Python

Python's `enum` module allows for the creation of enumerations, providing a way to define a set of named constants.

```python
from enum import Enum

class Status(Enum):  # Enum equivalent to C# Status type
    ACTIVE = "Active"
    INACTIVE = "Inactive"
    GRADUATED = "Graduated"

```

## Immutable DTO

The `dataclasses` module provides decorators and functions for automatically adding special methods such as `__init__()` and `__repr__()` to user-defined classes. By setting `frozen=True`, instances of the dataclass become immutable after creation.

```python
from dataclasses import dataclass
# Assuming Status enum is in src.core.status
from src.core.status import Status

@dataclass(frozen=True)
class Student:
    id: int
    first_name: str
    last_name: str
    grade_point: float
    status: Status

# Example Usage:
# student1 = Student(id=1, first_name="Jane", last_name="Doe",
#                    grade_point=3.8, status=Status.ACTIVE)
# student1.first_name = "Janet"  # This would raise FrozenInstanceError
```

---
See Also:
- [DTO - Data Transfer Object](DTO-Data-Transfer-Object.md) (general concept)
- [Transitioning from Classes to Records and Adopting Immutable Collections in C#](Transitioning-from-Classes-to-Records-and-Adopting-IImmutableList-in-C.md) (C# equivalent)
- [Functional in the Small and Object-Oriented in the Large](Functional-in-the-Small-and-Object-Oriented-in-the-Large.md) (immutability principle)