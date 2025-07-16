# Python Best Practices

## Table of Contents

- [Code Layout and Formatting](#code-layout-and-formatting)
- [Naming Conventions](#naming-conventions)
- [Function and Method Guidelines](#function-and-method-guidelines)
- [Class Guidelines](#class-guidelines)
- [Error Handling](#error-handling)
- [Package Organization](#package-organization)
- [Functional Programming](#functional-programming)
- [Libraries](#libraries)
- [Dependencies Management](#dependencies-management)
- [Virtual Environments](#virtual-environments)
- [Testing](#testing)
- [Performance Considerations](#performance-considerations)
- [Concurrency and Parallelism](#concurrency-and-parallelism)
- [Security Best Practices](#security-best-practices)
- [Version Control](#version-control)
- [Logging and Tracing](#logging-and-tracing)
- [Tools and Linting](#tools-and-linting)
- [Development Workflow](#development-workflow)
- [CI/CD Considerations](#cicd-considerations)
- [Inline Script Metadata](#inline-script-metadata)
- [General Best Practices](#general-best-practices)

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
import json

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

- Make all non-trivial APIs keyword-only
- Use named parameters
- Functions should do one thing well.
- Keep functions short and focused (typically under 20 lines).
- Use descriptive names that indicate what the function does.
- Include type hints for parameters and return values.
- Where possible, adopt a `functional programming` design philosophy where functions are idempotent and side effects are handled separately 
- Prefer `map`, `reduce`, and `filter` for list processing
- Prefer `itertools` iteration and looping instead of python comprehensions

```python
# Example:
def calculate_average(numbers: list[float]) -> float:
    """Calculate the average of a list of numbers."""
    if not numbers:
        raise ValueError("Cannot calculate average of empty list")
    return sum(numbers) / len(numbers)
```

### Type Hints

Use Python's type annotation system to make code more readable and maintainable:

```python
# Basic type hints
def greet(name: str) -> str:
    return f"Hello, {name}!"

# Complex type hints
from typing import Optional, Union, Dict, List, TypedDict, Callable

# Optional parameters
def process_data(data: Optional[dict] = None) -> None:
    if data is None:
        data = {}
    # Process the data...

# Union types
def convert_value(value: Union[str, int, float]) -> float:
    return float(value)

# Function type hints
def apply_operation(value: int, operation: Callable[[int], int]) -> int:
    return operation(value)

# TypedDict for structured dictionaries
class UserProfile(TypedDict):
    name: str
    age: int
    email: str
    active: bool

def get_user_email(profile: UserProfile) -> str:
    return profile["email"]
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

```python
def process_data(data: dict, options: Optional[dict] = None) -> list:
    """Process input data based on provided options.
    
    Args:
        data: Dictionary containing the input data to process.
        options: Optional configuration parameters. Defaults to None.
            If None, default options will be used.
            
    Returns:
        List of processed data items.
        
    Raises:
        ValueError: If data is empty or if options contains invalid keys.
        
    Examples:
        >>> process_data({"name": "John"})
        ["JOHN"]
        >>> process_data({"name": "John"}, {"lowercase": True})
        ["john"]
    """
    # Implementation...
```

## Class Guidelines

### Class Structure

- Each `Class` definition should be in a separate file
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
    
    def __str__(self) -> str:
        return f"{self.name} ({self._age})"
    
    def __repr__(self) -> str:
        return f"Person(name='{self._name}', age={self._age})"
```

## Error Handling

### Best Practices

- Use try/except blocks to handle specific exceptions.
- Avoid bare except clauses.
- Use context managers (`with` statements) when applicable.
- Raise exceptions early, handle them late.
- Include error messages that are helpful for debugging.

```python
# Example:
import json

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


## Package Organization

### Recommended 'src/' Style Layout

The best directory structure for a Python application should be organized, modular, and scalable to accommodate your input files, tests, logs, and modules. Below is a suggested directory structure:

```
my-python-app/
├── src/        
│   └── my_python_app/       # Application package
│       ├── __init__.py      # Makes it a package
│       ├── __main__.py      # Entry point of the application if called as python -m
│       ├── args.py          # Command line argument parsing
│       ├── cli.py           # Entry point for command line operation
│       ├── config.py        # Manage configuration settings
│       ├── mcp.py           # Entry point for Model Context Protocol (MCP) operation
│       ├── models           # Data models
│       └── ...
├── specs/                               # Program requirements and specifications
│   ├── FEATURES_TO_BE_DESIGNED.md`      # Master document listing program features 
│   ├── IMPLEMENT-TASK-001.md            # Implementation details for task 001
│   ├── IMPLEMENT-TASK-002.md            # Implementation details for task 002 
│   ├── IMPLEMENT-TASK-003.md            # Implementation details for task 003 
│   └── ...
├── tests/               # Test suite
│   ├── __init__.py
│   ├── test_main.py
│   ├── test_module1.py
│   ├── test_module2.py
│   └── ...
├── dist/                # Deployable builds
│   └── ...
├── docs/                # Documentation
│   ├── README.md
│   ├── CONTRIBUTING.md
│   └── ...
├── .env                 # Environment variables for this project
├── .gitignore           # Git ignore file for version control
├── pyproject.toml       # Project metadata 
├── requirements.txt     # List of Python dependencies
├── sample.cfg           # Sample runtime configuration file
├── uv.lock              # Installed python dependencies
└── README.md            # Project description
```

### Advantages of this Structure

- **Clarity and Organization**: Separates the codebase into well-defined areas, making it easier to locate and modify components.
- **Scalability**: Supports projects as they grow by maintaining a logical structure for adding new features or components.
- **Reusability**: Encourages modular design where utilities and components can be reused across the project.
- **Ease of Testing**: Includes a dedicated `tests/` directory, simplifying the management and discovery of test cases.
- **Documentation Support**: A `docs/` folder ensures that project documentation is kept organized and accessible.
- **Separation of Concerns:** Separation of Concerns (SoC) is a design principle that encourages dividing a software application into distinct sections, each responsible for a specific piece of functionality. This makes the codebase easier to understand, maintain, and extend by reducing the complexity of individual components and minimizing interdependencies.

### How to Work with This Structure

1. **Start with a Plan**: Before adding files, think about the module responsibilities and where they belong in the structure.
2. **Use** : Provide a high-level overview of the project, including setup instructions and key details.
3. **Leverage**: Define the project's dependencies clearly, and update it whenever new libraries are added.
4. **Test Coverage**: Ensure all modules in `project_name/` have corresponding tests in `tests/`.
5. **Document Changes**: Use the `docs/` folder to maintain changelogs, usage instructions, or API documentation.
6. **Commit Often**: When making changes, commit regularly with meaningful messages to keep the repository clean and navigable.

The \_\_main\_\_.py file is used in Python projects to define the entry point of the application when the module or package is executed as a script. It allows you to specify what should happen when the python -m package\_name command is executed. Here's an overview of its purpose and usage:

### Use \`\_\_main\_\_.py\` as the Entry Point for Execution

When a directory is structured as a Python package and contains an \_\_main\_\_.py file, running the package using \`python -m package\_name\` will execute the code in \_\_main\_\_.py. This is particularly useful for CLI (Command-Line Interface) tools or scripts.

- Keep \_\_main\_\_.py minimal and focused on bootstrapping the application logic, delegating most of the logic to other modules.
- For modularity, ensure that execution logic and reusable logic are separated, avoiding clutter in \_\_main\_\_.py.

This approach aligns with Python’s philosophy of enabling a module or package to behave both as a library and as an executable script.

### Module Guidelines

- Keep modules focused on a single responsibility
- Use `__init__.py` files to expose a clean public API
- Hide implementation details with underscore-prefixed private functions/classes

```python
# In __init__.py
from .user import User, create_user

# Hide implementation details
__all__ = ["User", "create_user"]
```

### Intra-Package Imports

Use relative imports within a package to maintain modularity:

```python
# In my_package/services/processor.py
from ..models.user import User
from ..utils.validators import validate_email
```

## Functional Programming

### Why Prefer Functional Approaches

Functional programming approaches in Python offer several advantages:

1. **Predictability**: Pure functions (no side effects) are easier to reason about
2. **Testability**: Functions with clear inputs and outputs are easier to test
3. **Composability**: Functions can be combined and reused more easily
4. **Concurrency**: Pure functions can run concurrently without race conditions

### Functional Techniques

```python
import functools
import itertools
import operator
from typing import Callable, Iterable, TypeVar

T = TypeVar('T')
R = TypeVar('R')

# Using map instead of list comprehension
numbers = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x**2, numbers))

# Using filter instead of conditional list comprehension
even_numbers = list(filter(lambda x: x % 2 == 0, numbers))

# Using reduce for aggregation
total = functools.reduce(operator.add, numbers, 0)

# Using itertools
first_10_squares = list(itertools.islice(map(lambda x: x**2, itertools.count(1)), 10))

# Function composition
def compose(*functions: Callable[[T], T]) -> Callable[[T], T]:
    def compose_two(f: Callable[[T], T], g: Callable[[T], T]) -> Callable[[T], T]:
        return lambda x: f(g(x))
    return functools.reduce(compose_two, functions, lambda x: x)

# Example use of composition
def add_one(x: int) -> int:
    return x + 1

def double(x: int) -> int:
    return x * 2

transform = compose(double, add_one)  # x → (x+1)*2
assert transform(3) == 8
```

## Libraries

### Core Recommended Libraries

#### Data Processing and Models

- **pydantic**: Type-safe data validation and parsing
  ```python
  from pydantic import BaseModel, Field, validator
  
  class User(BaseModel):
      id: int
      name: str
      email: str
      age: int = Field(gt=0, description="Age in years")
      
      @validator('email')
      def email_must_be_valid(cls, v):
          if '@' not in v:
              raise ValueError('must contain @ symbol')
          return v
      
  # Automatic validation
  user = User(id=1, name="John", email="john@example.com", age=30)
  ```

#### Logging

- **structlog**: Structured logging with modern features
  ```python
  import structlog

  # Configure once at program start
  structlog.configure(
      processors=[
          structlog.processors.add_log_level,
          structlog.processors.TimeStamper(fmt="iso"),
          structlog.processors.JSONRenderer(),
      ],
      logger_factory=structlog.PrintLoggerFactory(),
  )
  
  # Create a logger
  logger = structlog.get_logger()
  
  # Structured logging with context
  logger.info("User logged in", user_id=123, source_ip="192.168.1.1")
  logger.error("Operation failed", operation="data_sync", error_code=500)
  
  # Context can be built up incrementally
  log = logger.bind(request_id="req-123")
  log.info("Request started")
  
  # More context can be added
  log = log.bind(user_id=456)
  log.info("Request processing")
  
  # Output example:
  # {"level": "info", "timestamp": "2023-04-15T14:32:10.123Z", "event": "User logged in", "user_id": 123, "source_ip": "192.168.1.1"}
  ```

#### Database Access

- **sqlalchemy**: SQL toolkit and Object-Relational Mapping (ORM)
  ```python
  from sqlalchemy import create_engine, Column, Integer, String, select
  from sqlalchemy.ext.declarative import declarative_base
  from sqlalchemy.orm import sessionmaker

  Base = declarative_base()

  class User(Base):
      __tablename__ = 'users'
      
      id = Column(Integer, primary_key=True)
      name = Column(String)
      email = Column(String, unique=True)
      
  # Connect to database
  engine = create_engine('sqlite:///example.db')
  Base.metadata.create_all(engine)
  
  Session = sessionmaker(bind=engine)
  session = Session()
  
  # Create a user
  new_user = User(name='Alice', email='alice@example.com')
  session.add(new_user)
  session.commit()
  
  # Query users
  stmt = select(User).where(User.name == 'Alice')
  user = session.execute(stmt).scalar_one()
  print(f"Found user: {user.name} ({user.email})")
  ```

#### Utility Libraries

- **itertools**: Efficient iteration tools
  ```python
  import itertools
  
  # Create finite and infinite iterators
  cycles = itertools.cycle(["red", "green", "blue"])
  limited = itertools.islice(cycles, 5)  # Take just 5 items
  
  # Combine iterators
  names = ["Alice", "Bob", "Charlie"]
  ages = [25, 30, 35]
  for name, age in itertools.zip_longest(names, ages, fillvalue=0):
      print(f"{name}: {age}")
  
  # Group items
  data = [("A", 1), ("A", 2), ("B", 1), ("B", 2)]
  for key, group in itertools.groupby(data, lambda x: x[0]):
      print(f"{key}: {list(group)}")
  ```

- **xdg-base-dirs**: Access to XDG Base Directories
  ```python
  from xdg_base_dirs import xdg_config_home, xdg_data_home
  
  # Get standard paths for config and data
  config_path = xdg_config_home() / "myapp" / "config.toml"
  data_path = xdg_data_home() / "myapp" / "data"
  
  # Ensure directories exist
  config_path.parent.mkdir(parents=True, exist_ok=True)
  data_path.mkdir(parents=True, exist_ok=True)
  ```

### Additional Useful Libraries

- **mypy**: Static type checker
- **pytest**: Testing framework
- **ruff**: Super-fast Python linter and formatter
- **pathlib**: Object-oriented filesystem paths
- **dataclasses**: Data classes with less boilerplate
- **black**: Automatic code formatter (alternative to ruff format)
- **httpx**: Fully featured HTTP client for Python
- **mcp**: Model Context Protocol server

## Dependencies Management

### Using pyproject.toml

Modern Python projects should use `pyproject.toml` for dependency management and project configuration:

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "my-package"
version = "0.1.0"
description = "My awesome Python package"
readme = "README.md"
requires-python = ">=3.12"
license = {file = "LICENSE"}
authors = [
    {name = "Your Name", email = "your.email@example.com"},
]
dependencies = [
    "pydantic>=2.0.0",
    "structlog>=23.1.0",
    "sqlalchemy>=2.0.0",
    "xdg-base-dirs>=5.1.0",
]

[project.optional-dependencies]
dev = [
    "mypy>=1.0.0",
    "pytest>=7.0.0",
    "ruff>=0.1.0",
]

[tool.ruff]
line-length = 79
target-version = "py311"
select = ["E", "F", "B"]
ignore = []

[tool.ruff.format]
quote-style = "double"
indent-style = "space"
line-ending = "auto"

[tool.mypy]
python_version = "3.11"
warn_return_any = true
warn_unused_configs = true
disallow_untyped_defs = true
disallow_incomplete_defs = true

[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = "test_*.py"
python_functions = "test_*"
```

### Dependency Pinning

For application projects, pin exact dependency versions to ensure reproducible builds:

```
# requirements.txt - Generated with uv freeze
pydantic==2.5.2
structlog==23.2.0
sqlalchemy==2.0.23
xdg-base-dirs==5.1.0
```

## Virtual Environments

### Best Practices for Virtual Environments

- Use `uv` to manage virtual environments and project dependencies.
- To create a new virtual environment, run `uv venv` in the project root.
    - If a directory named `.venv` is present in the project root, this is not necessary
- Activate the virtual environment by running `source .venv/bin/activate` in the shell.
- Install project dependencies within the virtual environment using `uv pip install <module_name>`.
- Export dependencies to a `requirements.txt` file using `uv freeze`.
- Install dependencies from `requirements.txt` with `uv install -r requirements.txt`.

```bash
# Example: Run these commands in the project root
uv venv 
source .venv/bin/activate
uv pip install pydantic structlog
uv freeze > requirements.txt
deactivate
```

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

### Advanced Testing Techniques

#### Fixtures

Fixtures provide reusable test setup:

```python
import pytest
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

from myapp.models import Base, User

@pytest.fixture
def db_engine():
    # Use in-memory SQLite for tests
    engine = create_engine("sqlite:///:memory:")
    Base.metadata.create_all(engine)
    yield engine
    engine.dispose()

@pytest.fixture
def db_session(db_engine):
    Session = sessionmaker(bind=db_engine)
    session = Session()
    yield session
    session.close()

@pytest.fixture
def sample_user(db_session):
    user = User(name="Test User", email="test@example.com")
    db_session.add(user)
    db_session.commit()
    return user

def test_get_user(db_session, sample_user):
    # Test retrieving the user from the database
    retrieved_user = db_session.query(User).filter_by(name="Test User").first()
    assert retrieved_user is not None
    assert retrieved_user.email == "test@example.com"
```

#### Parametrization

Test multiple inputs with parametrization:

```python
import pytest

def is_palindrome(s: str) -> bool:
    """Check if a string is a palindrome."""
    s = s.lower().replace(" ", "")
    return s == s[::-1]

@pytest.mark.parametrize("input_str,expected", [
    ("radar", True),
    ("hello", False),
    ("A man a plan a canal Panama", True),
    ("race car", True),
    ("not a palindrome", False),
])
def test_is_palindrome(input_str, expected):
    assert is_palindrome(input_str) == expected
```

#### Mocking

Use mocking to isolate units of code:

```python
from unittest import mock
import requests

def get_user_data(user_id):
    """Fetch user data from an API."""
    response = requests.get(f"https://api.example.com/users/{user_id}")
    response.raise_for_status()
    return response.json()

def test_get_user_data():
    # Mock the requests.get function
    with mock.patch("requests.get") as mock_get:
        # Configure the mock
        mock_response = mock.Mock()
        mock_response.json.return_value = {"id": 123, "name": "Test User"}
        mock_response.raise_for_status.return_value = None
        mock_get.return_value = mock_response
        
        # Call the function with the mock in place
        result = get_user_data(123)
        
        # Verify the result
        assert result == {"id": 123, "name": "Test User"}
        
        # Verify the mock was called correctly
        mock_get.assert_called_once_with("https://api.example.com/users/123")
```

## Performance Considerations

### Optimization Guidelines

- Write for clarity first, optimize later if needed.
- Use appropriate data structures (lists vs. sets vs. dictionaries).
- Avoid premature optimization.
- Profile code before optimizing.
- Use generator expressions for large datasets.
- Consider using built-in functions and standard library tools.

### Performance Profiling

Use profiling tools to identify bottlenecks:

```python
import cProfile
import pstats

def profile_function(func, *args, **kwargs):
    """Profile a function and print stats."""
    profiler = cProfile.Profile()
    profiler.enable()
    
    result = func(*args, **kwargs)
    
    profiler.disable()
    stats = pstats.Stats(profiler).sort_stats('cumulative')
    stats.print_stats(20)  # Print top 20 time-consuming functions
    
    return result

# Usage example
def expensive_function(n):
    return sum(i**2 for i in range(n))

profile_function(expensive_function, 1000000)
```

## Concurrency and Parallelism

### Threading vs. Multiprocessing vs. AsyncIO

Choose the right concurrency model based on your workload:

#### Threading

Use for I/O-bound tasks where operations spend time waiting:

```python
import threading
import time
from typing import List

def download_file(url: str) -> None:
    """Simulate downloading a file."""
    print(f"Downloading {url}...")
    time.sleep(2)  # Simulate download time
    print(f"Downloaded {url}")

def download_files_concurrently(urls: List[str]) -> None:
    """Download multiple files using threads."""
    threads = []
    for url in urls:
        thread = threading.Thread(target=download_file, args=(url,))
        threads.append(thread)
        thread.start()
    
    # Wait for all threads to complete
    for thread in threads:
        thread.join()

# Example usage
urls = [f"https://example.com/file{i}" for i in range(5)]
download_files_concurrently(urls)
```

#### Multiprocessing

Use for CPU-bound tasks that need parallel computation:

```python
import multiprocessing
from typing import List

def process_chunk(data: List[int]) -> int:
    """Process a chunk of data."""
    return sum(x * x for x in data)

def parallel_processing(data: List[int], num_processes: int = None) -> int:
    """Process data in parallel using multiple processes."""
    if num_processes is None:
        num_processes = multiprocessing.cpu_count()
    
    # Split data into chunks
    chunk_size = len(data) // num_processes
    chunks = [data[i:i+chunk_size] for i in range(0, len(data), chunk_size)]
    
    # Process chunks in parallel
    with multiprocessing.Pool(processes=num_processes) as pool:
        results = pool.map(process_chunk, chunks)
    
    return sum(results)

# Example usage
data = list(range(10000000))
result = parallel_processing(data)
print(f"Result: {result}")
```

#### AsyncIO

Use for I/O-bound tasks when you need to handle many concurrent operations:

```python
import asyncio
import aiohttp
from typing import List

async def fetch_url(session, url: str) -> str:
    """Fetch content from a URL asynchronously."""
    async with session.get(url) as response:
        return await response.text()

async def fetch_all_urls(urls: List[str]) -> List[str]:
    """Fetch multiple URLs concurrently."""
    async with aiohttp.ClientSession() as session:
        tasks = [fetch_url(session, url) for url in urls]
        return await asyncio.gather(*tasks)

# Example usage
async def main():
    urls = [f"https://example.com/page{i}" for i in range(10)]
    results = await fetch_all_urls(urls)
    print(f"Fetched {len(results)} pages")

# Run the async function
asyncio.run(main())
```

## Security Best Practices

### Input Validation

Always validate and sanitize input data:

```python
from pydantic import BaseModel, EmailStr, validator

class UserRegistration(BaseModel):
    username: str
    email: EmailStr  # Validates email format
    password: str
    age: int
    
    @validator('username')
    def username_alphanumeric(cls, v):
        if not v.isalnum():
            raise ValueError('must be alphanumeric')
        return v
    
    @validator('password')
    def password_min_length(cls, v):
        if len(v) < 8:
            raise ValueError('must be at least 8 characters')
        return v
    
    @validator('age')
    def age_reasonable(cls, v):
        if v < 18 or v > 120:
            raise ValueError('must be between 18 and 120')
        return v
```

### Secrets Management

Never hardcode secrets or credentials:

```python
import os
from pathlib import Path
import tomli

def load_secrets(env_prefix="APP_"):
    """Load secrets from environment variables or a secrets file."""
    # First check environment variables
    db_password = os.environ.get(f"{env_prefix}DB_PASSWORD")
    api_key = os.environ.get(f"{env_prefix}API_KEY")
    
    # If not found in environment, try a secrets file
    if not db_password or not api_key:
        secrets_path = Path.home() / ".secrets" / "myapp.toml"
        if secrets_path.exists():
            with open(secrets_path, "rb") as f:
                secrets = tomli.load(f)
                db_password = db_password or secrets.get("db_password")
                api_key = api_key or secrets.get("api_key")
    
    return {
        "db_password": db_password,
        "api_key": api_key,
    }
```

### Secure Coding Practices

- Use secure hashing for passwords:
  ```python
  import hashlib
  import os
  
  def hash_password(password: str) -> tuple[str, str]:
      """Hash a password with a random salt."""
      salt = os.urandom(32)
      key = hashlib.pbkdf2_hmac(
          'sha256',
          password.encode('utf-8'),
          salt,
          100000,  # Number of iterations
      )
      return salt.hex(), key.hex()
  
  def verify_password(stored_salt: str, stored_key: str, password: str) -> bool:
      """Verify a password against a stored hash and salt."""
      salt = bytes.fromhex(stored_salt)
      key = hashlib.pbkdf2_hmac(
          'sha256',
          password.encode('utf-8'),
          salt,
          100000,  # Must match the number of iterations used for hashing
      )
      return key.hex() == stored_key
  ```

- Sanitize SQL queries with parameterized statements:
  ```python
  # BAD - vulnerable to SQL injection
  def get_user_unsafe(username):
      cursor.execute(f"SELECT * FROM users WHERE username = '{username}'")
      
  # GOOD - use parameterized queries
  def get_user_safe(username):
      cursor.execute("SELECT * FROM users WHERE username = ?", (username,))
  ```

## Version Control

### Git Best Practices

- Write clear, descriptive commit messages.
- Make small, focused commits.
- Use feature branches for new development.
- Keep the main/master branch stable.
- Review code before merging.

### Example Workflow

```bash
# Start a new feature
git checkout -b feature/new-login-ui

# Make changes and commit
git add .
git commit -m "Add new responsive login UI components"

# Keep feature branch up to date with main
git fetch origin
git rebase origin/main

# Push feature branch to remote
git push -u origin feature/new-login-ui

# After code review, merge to main
git checkout main
git merge --no-ff feature/new-login-ui
git push origin main

# Delete feature branch after merge
git branch -d feature/new-login-ui
git push origin --delete feature/new-login-ui
```

## Logging and Tracing

### Best Practices for Logging

- Use the `structlog` module for logging instead of print statements.
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

### Structlog Example

```python
import structlog
from typing import Optional, Dict, Any

# Configure structlog once at application startup
structlog.configure(
    processors=[
        # Add a timestamp in ISO8601 format
        structlog.processors.TimeStamper(fmt="iso"),
        # Add log level to the output
        structlog.processors.add_log_level,
        # Format the exception info if it's present
        structlog.processors.format_exc_info,
        # Render the final output as JSON
        structlog.processors.JSONRenderer(),
    ],
    # Use print for output by default
    logger_factory=structlog.PrintLoggerFactory(),
    # Cache logger instances
    cache_logger_on_first_use=True,
)

class PaymentProcessor:
    def __init__(self, merchant_id: str):
        # Create a logger with context
        self.logger = structlog.get_logger().bind(
            component="PaymentProcessor",
            merchant_id=merchant_id
        )
        self.logger.info("Initialized payment processor")
    
    def process_payment(self, amount: float, user_id: str,
                        payment_method: Dict[str, Any]) -> bool:
        """Process a payment transaction."""
        # Add transaction context to the logger
        tx_logger = self.logger.bind(
            amount=amount,
            user_id=user_id,
            payment_method_type=payment_method.get("type"),
            transaction_id=generate_transaction_id()
        )
        
        tx_logger.info("Starting payment processing")
        
        try:
            # Payment processing logic...
            if amount <= 0:
                tx_logger.error("Invalid payment amount", reason="negative_or_zero")
                return False
                
            tx_logger.info("Payment validated")
            
            # More processing...
            
            # Log masked card details (only last 4 digits)
            if payment_method.get("type") == "credit_card":
                card_number = payment_method.get("card_number", "")
                masked_number = f"****-****-****-{card_number[-4:]}" if len(card_number) >= 4 else "****"
                tx_logger.info("Processing credit card", 
                              card_last_four=card_number[-4:] if len(card_number) >= 4 else None,
                              expiry=payment_method.get("expiry"))
            
            # Success case
            tx_logger.info("Payment processed successfully")
            return True
            
        except Exception as e:
            # Log the exception (structlog will format it)
            tx_logger.exception("Payment processing failed", 
                               error_type=type(e).__name__)
            return False
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
- Lint code with `ruff`.
- Format code with `ruff`.
- Check style with `ruff`.

### Configuration

Include a `pyproject.toml` file to maintain consistent settings across the project.

```toml
[tool.ruff]
# Ruff configuration
line-length = 79
target-version = "py311"
select = [
    "E",   # pycodestyle errors
    "F",   # pyflakes
    "B",   # flake8-bugbear
    "I",   # isort
    "C4",  # flake8-comprehensions
    "UP",  # pyupgrade
    "N",   # pep8-naming
    "SIM", # flake8-simplify
    "TID", # flake8-tidy-imports
]
ignore = []

[tool.ruff.format]
quote-style = "double"
indent-style = "space"
line-ending = "auto"

[tool.mypy]
python_version = "3.11"
warn_return_any = true
warn_unused_configs = true
disallow_untyped_defs = true
disallow_incomplete_defs = true
check_untyped_defs = true
disallow_untyped_decorators = true
no_implicit_optional = true
strict_optional = true

[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = "test_*.py"
python_functions = "test_*"
```

## Development Workflow

### Recommended Development Flow

1. **Setup**: Clone repository and set up virtual environment
   ```bash
   git clone https://github.com/org/repo.git
   cd repo
   uv venv
   source .venv/bin/activate
   uv pip install -e ".[dev]"
   ```

2. **Feature Development**: Create a feature branch and implement changes
   ```bash
   git checkout -b feature/new-feature
   
   # Implement changes...
   
   # Lint and format code
   ruff check .
   ruff format .
   
   # Run tests
   pytest
   ```

3. **Code Review**: Commit changes and create a pull request
   ```bash
   git add .
   git commit -m "Add new feature: detailed description"
   git push -u origin feature/new-feature
   
   # Create pull request via web UI or CLI
   ```

4. **Integration**: After review and approval, merge to main branch
   ```bash
   git checkout main
   git pull
   git merge --no-ff feature/new-feature
   git push
   ```

### Git Workflow Diagram

```
main       --o------o------------o---------------o---->
             \      \            /               /
feature A     ------o--o--o--o--               /
                      \              /
feature B              --o--o--o--o--
```

## CI/CD Considerations

### Continuous Integration

Set up CI pipelines to ensure code quality:

```yaml
# Example GitHub Actions workflow
name: Python CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'
          
      - name: Install dependencies
        run: |
          pip install uv
          uv venv
          source .venv/bin/activate
          uv pip install -e ".[dev]"
          
      - name: Lint with ruff
        run: |
          source .venv/bin/activate
          ruff check .
          
      - name: Type check with mypy
        run: |
          source .venv/bin/activate
          mypy src tests
          
      - name: Test with pytest
        run: |
          source .venv/bin/activate
          pytest --cov=src
          
      - name: Upload coverage report
        uses: codecov/codecov-action@v3
```

### Deployment Automation

Automate deployment with CI/CD:

```yaml
# Example deployment workflow
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'
          
      - name: Install dependencies
        run: |
          pip install uv
          uv venv
          source .venv/bin/activate
          uv pip install -e .
          
      - name: Build package
        run: |
          source .venv/bin/activate
          pip install build
          python -m build
          
      - name: Deploy to PyPI
        uses: pypa/gh-action-pypi-publish@release/v1
        with:
          password: ${{ secrets.PYPI_API_TOKEN }}
```

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

## General Best Practices

- Use strict type checking.
- Write custom exceptions that provide meaningful information to the developer.
- Document functions, including parameters and return values.
- The maximum file length is 500 lines. If a file is longer than 500 lines, refactor 
  it and consider splitting into multiple files.
- Design functions to be idempotent when possible.
- Separate pure functions from functions with side effects.
- Use meaningful variable and function names that clearly convey their purpose.
- Write self-documenting code that is easy to understand and maintain.
- Store each class definition in a separate file
- Create custom exception classes
- Avoid bare exceptions
