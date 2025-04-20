# Python Best Practices

## Code Layout and Formatting

### Indentation and Line Length

- Use 4 spaces for indentation (never tabs).
- Limit all lines to a maximum of 79 characters for code.
- Limit docstrings and comments to 72 characters.
- Use line breaks between logical sections of code.

### Imports

- Imports should be on separate lines.
- Order imports in three groups, separated by a blank line:
  1. Standard library imports
  2. Third-party library imports
  3. Local application imports
- Use absolute imports over relative imports.
- Avoid wildcard imports (`from module import *`).

```python
# Example:
import os
import sys

import numpy as np
import pandas as pd

from mypackage import mymodule
```

## Naming Conventions

### General Rules

- Names should be descriptive and meaningful.
- Avoid single-letter names except for counters or iterators.
- Use English names for all identifiers.

### Specific Conventions

- **Functions**: lowercase with underscores (`calculate_total`).
- **Variables**: lowercase with underscores (`user_input`).
- **Classes**: CapWords convention (`CustomerOrder`).
- **Constants**: uppercase with underscores (`MAX_CONNECTIONS`).
- **Protected instance attributes**: single leading underscore (`_internal_value`).
- **Private instance attributes**: double leading underscore (`__private_method`).
- **Module names**: short, lowercase (`util.py`).

## Function and Method Guidelines

### Function Design

- Functions should do one thing well.
- Keep functions short and focused (typically under 20 lines).
- Use descriptive names that indicate what the function does.
- Include type hints for parameters and return values.

```python
# Example:
def calculate_average(numbers: list[float]) -> float:
    """Calculate the average of a list of numbers."""
    if not numbers:
        raise ValueError("Cannot calculate average of empty list")
    return sum(numbers) / len(numbers)
```

### Documentation

- Use docstrings for all public modules, functions, classes, and methods.
- Follow Google-style docstring format.
- Include:
  - Brief description
  - Args/Parameters
  - Returns
  - Raises (if applicable)
  - Examples (when helpful).

## Class Guidelines

### Class Structure

- Use class attributes for class-wide constants.
- Define `__init__` method first.
- Group methods by functionality.
- Use properties instead of getter/setter methods.
- Implement special methods as needed (`__str__`, `__repr__`, etc.).

```python
# Example:
class Person:
    """Represents a person with basic attributes."""
    
    def __init__(self, name: str, age: int):
        self._name = name
        self._age = age
    
    @property
    def name(self) -> str:
        return self._name
    
    def __str__() -> str:
        return f"{self.name} ({self._age})"
```

## Error Handling

### Best Practices

- Use try/except blocks to handle specific exceptions.
- Avoid bare except clauses.
- Use context managers (with statements) when applicable.
- Raise exceptions early, handle them late.
- Include error messages that are helpful for debugging.

```python
# Example:
def read_config(filename: str) -> dict:
    try:
        with open(filename, 'r') as f:
            return json.load(f)
    except FileNotFoundError:
        raise FileNotFoundError(f"Config file '{filename}' not found")
    except json.JSONDecodeError as e:
        raise ValueError(f"Invalid JSON in config file: {e}")
```

### Creating Custom Exception Classes

Creating custom exception classes can improve error handling by making your code more expressive and easier to debug.

- Derive custom exceptions from Python's built-in `Exception` class.
- Use descriptive names for exception classes that clearly indicate the type of error.
- Include meaningful error messages and additional attributes if necessary to provide more context.

```python
# Example:
class InvalidConfigurationError(Exception):
    """Raised when the configuration is invalid."""
    def __init__(self, message: str, config_path: str):
        super().__init__(message)
        self.config_path = config_path

try:
    raise InvalidConfigurationError("Invalid configuration file", "/path/to/config.json")
except InvalidConfigurationError as e:
    print(f"Error: {e}. Path: {e.config_path}")
```

## Virtual Environments

### Best Practices for Virtual Environments

- Use `uv` to manage virtual environments and project dependencies.
- Create a new virtual environment with `uv venv create <env_name>`.
- Activate the virtual environment with `uv activate <env_name>`.
- Install project dependencies within the virtual environment using `uv install`.
- Deactivate the virtual environment with `uv deactivate`.
- Export dependencies to a `requirements.txt` file using `uv freeze`.
- Install dependencies from `requirements.txt` with `uv install -r requirements.txt`.

```bash
# Example:
uv venv create myenv
uv activate myenv
uv install numpy pandas
uv freeze > requirements.txt
uv deactivate
```

## Reading Configuration Values from a TOML Config File

### Using TOML for Configuration

TOML is a simple and human-readable configuration format that is widely used in Python projects. It is especially useful for maintaining structured and nested configuration data.

### Best Practices

- Store all configurable parameters in a `config.toml` file at the root of your project.
- Use the `tomllib` module (Python 3.11+) or a third-party library like `toml` to read the configuration.
- Validate configuration values before using them in your application.
- Provide default values or fallbacks for optional settings.

### Example Configuration File (`config.toml`)

```toml
[database]
host = "localhost"
port = 5432
user = "admin"
password = "password"
database_name = "myapp_db"

[logging]
level = "INFO"
log_file = "logs/app.log"
```

### Reading Configuration in Python

```python
# Example:
import tomllib

# Load configuration from the TOML file
def load_config(config_file: str) -> dict:
    try:
        with open(config_file, 'rb') as f:
            config = tomllib.load(f)
        return config
    except FileNotFoundError:
        raise FileNotFoundError(f"Configuration file '{config_file}' not found.")
    except tomllib.TOMLDecodeError as e:
        raise ValueError(f"Invalid TOML in configuration file: {e}")

# Using the configuration
config = load_config("config.toml")

# Access specific settings
db_config = config["database"]
logging_config = config["logging"]

print(f"Database host: {db_config['host']}")
print(f"Log level: {logging_config['level']}")
```

### Advantages

- **Readability**: TOML is easy to write and understand, even for complex configurations.
- **Structured Data**: Supports nested structures, making it suitable for organizing related settings.
- **Maintainability**: Separates configuration from code, simplifying updates and deployment.

## Testing

### Testing Guidelines

- Write tests for all new code.
- Use pytest as the testing framework.
- Follow the Arrange-Act-Assert pattern.
- Test both success and failure cases.
- Use meaningful test names that describe the scenario.

```python
# Example:
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

- Write for clarity first, optimize later if needed.
- Use appropriate data structures (lists vs. sets vs. dictionaries).
- Avoid premature optimization.
- Profile code before optimizing.
- Use generator expressions for large datasets.
- Consider using built-in functions and standard library tools.

## Version Control

### Git Best Practices

- Write clear, descriptive commit messages.
- Make small, focused commits.
- Use feature branches for new development.
- Keep the main/master branch stable.
- Review code before merging.

## Logging and Tracing

### Best Practices for Logging

- Use Python's built-in `logging` module instead of print statements.
- Configure logging levels appropriately:
    - `DEBUG`: Detailed information, for diagnosing issues.
    - `INFO`: Confirmation that things are working as expected.
    - `WARNING`: An indication of something unexpected or that may cause problems in the future.
    - `ERROR`: A more serious problem, the program can continue running.
    - `CRITICAL`: A serious error, indicating the program may not be able to continue running.
- Use log files to persist logs for later analysis.
- Write logs in a structured format such as JSONL (JSON Lines), where each log entry is a separate JSON object on its own line. This format is easy to parse and works well with many log aggregation tools. For example:

```
{"timestamp": "2025-01-01T12:00:00Z", "level": "INFO", "message": "Application started"}
{"timestamp": "2025-01-01T12:05:00Z", "level": "ERROR", "message": "An error occurred", "details": "File not found"}
```

- Include timestamps, log levels, and context in log messages.
- Avoid logging sensitive information such as passwords, API keys, personally identifiable information (PII), or other confidential data. For example, ensure logs do not include fields like `password="mysecretpassword"` or `credit_card="1234-5678-9012-3456"`. Mask or omit sensitive data where necessary to maintain security and compliance.
- Use external tools like `Sentry` or `Logstash` for error tracking and centralized logging.

```python
# Example:
import logging

logging.basicConfig(level=logging.INFO,
                    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')

logger = logging.getLogger(__name__)

logger.debug("This is a debug message")
logger.info("This is an info message")
logger.warning("This is a warning message")
logger.error("This is an error message")
logger.critical("This is a critical message")
```

### Advantages of Logging and Tracing

- **Debugging**: Easier to pinpoint issues with detailed logs and trace information.
- **Monitoring**: Helps in understanding application behavior and identifying performance bottlenecks.
- **Accountability**: Maintains a record of system behavior for audits or reviews.
- **Scalability**: Essential for diagnosing issues in large, distributed systems.

## Tools and Linting

### Recommended Tools

- Use `uv` for package management and installation.
- Use type checking with `mypy`.
- Lint code `ruff`
- Format code with `ruff`.
- Check style with `ruff`.

### Configuration

Include a `pyproject.toml` file to maintain consistent settings across the project.

## Inline Script Metadata

### Guidelines

- Include the PEP 723 TOML metadata header at the beginning of scripts.
- The first line of the PEP 723 header is always `# /// script`.
- Use inline script metadata to specify dependencies.

```python
# Example:
# /// script
# requires-python = ">=3.11"
# dependencies = [
#   "numpy",
#   "pandas"
# ]
# ///

def main():
    # Add your code here
    ...

if __name__ == "__main__":
    main()
```

### Best Practices

- Use strict type checking.
- Write custom exceptions that provide meaningful information to the developer.
- Document functions, including parameters and return values.


