# Python Coding Conventions and Best Practices

## Code Layout and Formatting

### Indentation and Line Length
- Use 4 spaces for indentation (never tabs)
- Limit all lines to a maximum of 79 characters for code
- Limit docstrings/comments to 72 characters
- Use line breaks between logical sections of code

### Imports
- Imports should be on separate lines
- Order imports in three groups, separated by a blank line:
    1. Standard library imports
    2. Third-party library imports
    3. Local application imports
- Use absolute imports over relative imports
- Avoid wildcard imports (`from module import *`)

```python
# Good
import os
import sys

import numpy as np
import pandas as pd

from mypackage import mymodule
```

## Naming Conventions

### General Rules
- Names should be descriptive and meaningful
- Avoid single-letter names except for counters or iterators
- Use English names for all identifiers

### Specific Conventions
- Functions: lowercase with underscores (`calculate_total`)
- Variables: lowercase with underscores (`user_input`)
- Classes: CapWords convention (`CustomerOrder`)
- Constants: uppercase with underscores (`MAX_CONNECTIONS`)
- Protected instance attributes: single leading underscore (`_internal_value`)
- Private instance attributes: double leading underscore (`__private_method`)
- Module names: short, lowercase (`util.py`)

## Function and Method Guidelines

### Function Design
- Functions should do one thing well
- Keep functions short and focused (typically under 20 lines)
- Use descriptive names that indicate what the function does
- Include type hints for parameters and return values

```python
def calculate_average(numbers: list[float]) -> float:
    """Calculate the average of a list of numbers."""
    if not numbers:
        raise ValueError("Cannot calculate average of empty list")
    return sum(numbers) / len(numbers)
```

### Documentation
- Use docstrings for all public modules, functions, classes, and methods
- Follow Google-style docstring format
- Include:
    - Brief description
    - Args/Parameters
    - Returns
    - Raises (if applicable)
    - Examples (when helpful)

## Class Guidelines

### Class Structure
- Use class attributes for class-wide constants
- Define `__init__` method first
- Group methods by functionality
- Use properties instead of getter/setter methods
- Implement special methods as needed (`__str__`, `__repr__`, etc.)

```python
class Person:
    """Represents a person with basic attributes."""
    
    def __init__(self, name: str, age: int):
        self._name = name
        self._age = age
    
    @property
    def name(self) -> str:
        return self._name
    
    def __str__(self) -> str:
        return f"{self.name} ({self._age})"
```

## Error Handling

### Best Practices
- Use try/except/finally blocks to handle specific exceptions
- Avoid bare except clauses
- Use context managers (with statements) when applicable
- Raise exceptions early, handle them late
- Create custom ecxeption classes
- Include error messages that are helpful for debugging

```python
def read_config(filename: str) -> dict:
    try:
        with open(filename, 'r') as f:
            return json.load(f)
    except FileNotFoundError:
        raise FileNotFoundError(f"Config file '{filename}' not found")
    except json.JSONDecodeError as e:
        raise ValueError(f"Invalid JSON in config file: {e}")
```

## Testing

### Testing Guidelines
- Write tests for all new code
- Use pytest as the testing framework
- Follow the Arrange-Act-Assert pattern
- Test both success and failure cases
- Use meaningful test names that describe the scenario

```python
def test_calculate_average_with_valid_input():
    # Arrange
    numbers = [1, 2, 3, 4, 5]
    
    # Act
    result = calculate_average(numbers)
    
    # Assert
    assert result == 3.0
```

## Performance Considerations

### Optimization Guidelines
- Write for clarity first, optimize later if needed
- Use appropriate data structures (lists vs. sets vs. dictionaries)
- Avoid premature optimization
- Profile code before optimizing
- Use generator expressions for large datasets
- Consider using built-in functions and standard library tools

## Version Control

### Git Best Practices
- Write clear, descriptive commit messages
- Make small, focused commits
- Use feature branches for new development
- Keep the main/master branch stable
- Review code before merging

## Project Structure

### Recommended Layout
```
project_name/
├── README.md
├── requirements.txt
├── setup.py
├── project_name/
│   ├── __init__.py
│   ├── core.py
│   ├── utils/
│   └── tests/
└── docs/
```

## Tools and Linting

### Recommended Tools
- Use type checking with mypy
- Format code with black
- Check style with flake8
- Sort imports with isort
- Use pylint for additional static analysis

### Configuration
Include a `pyproject.toml` file to maintain consistent settings across the project.
