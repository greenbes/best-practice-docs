# Python Testing Strategy and Guidelines

## Table of Contents

- [Testing Philosophy and Mindset](#testing-philosophy-and-mindset)
- [Core Testing Principles](#core-testing-principles)
- [Python Testing Tools and Frameworks](#python-testing-tools-and-frameworks)
- [Designing Testable Code](#designing-testable-code)
- [Step-by-Step Guide: Writing New Tests](#step-by-step-guide-writing-new-tests)
- [Step-by-Step Guide: Evaluating Existing Tests](#step-by-step-guide-evaluating-existing-tests)
- [Supporting Tools and Practices](#supporting-tools-and-practices)
- [Advanced Testing Strategies](#advanced-testing-strategies)

## Testing Philosophy and Mindset

### Why We Test

Testing serves multiple critical purposes beyond simply finding bugs:

1. **Documentation**: Tests document how code is intended to be used
2. **Design Feedback**: Difficulty in testing often indicates design problems
3. **Regression Prevention**: Tests ensure existing functionality remains intact
4. **Confidence**: Tests provide confidence when refactoring or adding features
5. **Living Specification**: Tests serve as executable specifications of behavior

### The Testing Pyramid

The testing pyramid guides us in balancing different types of tests:

```
          /\
         /  \  E2E Tests (5%)
        /----\
       /      \ Integration Tests (15%)
      /--------\
     /          \ Unit Tests (80%)
    /____________\
```

- **Unit Tests**: Fast, isolated, numerous. Test individual functions/methods
- **Integration Tests**: Test component interactions, database operations, API calls
- **E2E Tests**: Test complete user workflows, slowest but most realistic

### Test-Driven Development (TDD)

Follow the Red-Green-Refactor cycle:

1. **Red**: Write a failing test for the desired behavior
2. **Green**: Write minimal code to make the test pass
3. **Refactor**: Improve the code while keeping tests green

Benefits:
- Forces you to think about interface before implementation
- Ensures all code has tests
- Keeps implementation simple and focused

### Testing Confidence vs Coverage

- **Coverage** measures what percentage of code is executed by tests
- **Confidence** measures how sure we are the system works correctly
- High coverage doesn't guarantee high confidence
- Focus on testing critical paths and edge cases, not achieving 100% coverage

## Core Testing Principles

### How to Know We're Testing the Right Things

1. **Business Value Alignment**
   - Test features that directly impact users
   - Prioritize tests for revenue-generating features
   - Focus on critical user journeys

2. **Risk Assessment Matrix**
   ```
   High Impact + High Probability = Test Extensively
   High Impact + Low Probability = Test Core Scenarios
   Low Impact + High Probability = Basic Tests
   Low Impact + Low Probability = Consider Not Testing
   ```

3. **Code Criticality Indicators**
   - Complex business logic
   - Security-sensitive operations
   - Data transformation logic
   - External integrations
   - Performance-critical paths

### How to Verify Tests Are Correct

1. **Test the Tests**
   - Temporarily break the implementation - tests should fail
   - Use mutation testing to verify test effectiveness
   - Review tests during code review

2. **Test Characteristics of Good Tests**
   - **Fast**: Execute quickly (milliseconds for unit tests)
   - **Independent**: No dependencies between tests
   - **Repeatable**: Same result every time
   - **Self-Validating**: Clear pass/fail result
   - **Timely**: Written close to production code

3. **Common Test Smells**
   - Tests that never fail
   - Tests requiring specific execution order
   - Tests with complex setup
   - Tests testing implementation details
   - Flaky tests that fail intermittently

### Testing at the Correct Level

Choose the appropriate test level based on what you're verifying:

| What to Test | Test Level | Example |
|-------------|------------|---------|
| Algorithm correctness | Unit | Sorting function |
| Business logic | Unit | Tax calculation |
| Database queries | Integration | User repository |
| API contracts | Integration | REST endpoints |
| User workflows | E2E | Purchase flow |
| Performance requirements | Performance | Load testing |

## Python Testing Tools and Frameworks

### Core Testing Framework: pytest

pytest is the recommended testing framework for Python projects:

```python
# Installation
pip install pytest pytest-cov pytest-mock pytest-xdist

# Basic test structure
def test_user_creation():
    user = User(name="Alice", email="alice@example.com")
    assert user.name == "Alice"
    assert user.email == "alice@example.com"
```

### Essential Testing Tools

1. **Coverage Analysis**
   ```bash
   # Run tests with coverage
   pytest --cov=src --cov-report=html --cov-report=term
   
   # Coverage configuration in pyproject.toml
   [tool.coverage.run]
   source = ["src"]
   omit = ["*/tests/*", "*/migrations/*"]
   
   [tool.coverage.report]
   precision = 2
   show_missing = true
   skip_covered = false
   ```

2. **Mocking and Patching**
   ```python
   from unittest.mock import Mock, patch
   import pytest
   
   def test_external_api_call():
       with patch('requests.get') as mock_get:
           mock_get.return_value.json.return_value = {"status": "ok"}
           result = check_service_status()
           assert result == "ok"
           mock_get.assert_called_once_with("https://api.example.com/status")
   ```

3. **Property-Based Testing with Hypothesis**
   ```python
   from hypothesis import given, strategies as st
   
   @given(st.lists(st.integers()))
   def test_sort_is_idempotent(lst):
       assert sorted(sorted(lst)) == sorted(lst)
   
   @given(st.text())
   def test_decode_encode_inverse(text):
       assert decode(encode(text)) == text
   ```

4. **Mutation Testing with mutmut**
   ```bash
   # Install and run mutation testing
   pip install mutmut
   mutmut run --paths-to-mutate=src/
   mutmut results
   ```

5. **Test Fixtures and Factories**
   ```python
   import pytest
   from datetime import datetime
   
   @pytest.fixture
   def user_factory():
       def make_user(**kwargs):
           defaults = {
               "name": "Test User",
               "email": "test@example.com",
               "created_at": datetime.utcnow()
           }
           defaults.update(kwargs)
           return User(**defaults)
       return make_user
   
   def test_user_age_calculation(user_factory):
       user = user_factory(birth_date=datetime(1990, 1, 1))
       assert user.age >= 33  # Depends on current year
   ```

### Testing Configuration

```toml
# pyproject.toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = "test_*.py"
python_classes = "Test*"
python_functions = "test_*"
addopts = [
    "--strict-markers",
    "--tb=short",
    "--cov=src",
    "--cov-branch",
    "--cov-report=term-missing:skip-covered",
    "--cov-fail-under=80",
]
markers = [
    "slow: marks tests as slow (deselect with '-m \"not slow\"')",
    "integration: marks tests as integration tests",
    "unit: marks tests as unit tests",
]
```

## Designing Testable Code

### Dependency Injection

Make dependencies explicit and injectable:

```python
# Bad: Hard-coded dependency
class EmailService:
    def send(self, message):
        smtp_client = SMTPClient("smtp.gmail.com", 587)  # Hard to test
        smtp_client.send(message)

# Good: Injectable dependency
class EmailService:
    def __init__(self, smtp_client: SMTPClient):
        self.smtp_client = smtp_client
    
    def send(self, message):
        self.smtp_client.send(message)

# In tests
def test_email_service():
    mock_smtp = Mock(spec=SMTPClient)
    service = EmailService(mock_smtp)
    service.send("Hello")
    mock_smtp.send.assert_called_once_with("Hello")
```

### Pure Functions and Side Effect Management

Separate pure logic from side effects:

```python
# Bad: Mixed concerns
def process_order(order_id):
    order = db.get_order(order_id)  # Side effect
    if order.total > 100:           # Business logic
        order.discount = 0.1
    db.save_order(order)            # Side effect
    email_service.send_confirmation(order)  # Side effect

# Good: Separated concerns
def calculate_order_discount(order: Order) -> float:
    """Pure function - easy to test"""
    if order.total > 100:
        return 0.1
    return 0.0

def process_order(order_id: str, db: Database, email_service: EmailService):
    """Orchestration function with injected dependencies"""
    order = db.get_order(order_id)
    order.discount = calculate_order_discount(order)
    db.save_order(order)
    email_service.send_confirmation(order)
```

### Interface Design for Testability

Use protocols and abstract base classes:

```python
from typing import Protocol
from abc import ABC, abstractmethod

# Protocol-based interface
class StorageProtocol(Protocol):
    def save(self, key: str, value: str) -> None: ...
    def load(self, key: str) -> str: ...

# Abstract base class
class Storage(ABC):
    @abstractmethod
    def save(self, key: str, value: str) -> None:
        pass
    
    @abstractmethod
    def load(self, key: str) -> str:
        pass

# Testable implementation
class CacheService:
    def __init__(self, storage: Storage):
        self.storage = storage
    
    def get_or_compute(self, key: str, compute_fn):
        try:
            return self.storage.load(key)
        except KeyError:
            value = compute_fn()
            self.storage.save(key, value)
            return value
```

### Configuration and Environment Management

Make configuration testable:

```python
from dataclasses import dataclass
from typing import Optional

@dataclass
class AppConfig:
    database_url: str
    api_key: str
    debug: bool = False
    timeout: int = 30
    
    @classmethod
    def from_env(cls, env: dict) -> 'AppConfig':
        """Create config from environment variables"""
        return cls(
            database_url=env["DATABASE_URL"],
            api_key=env["API_KEY"],
            debug=env.get("DEBUG", "false").lower() == "true",
            timeout=int(env.get("TIMEOUT", "30"))
        )

# In tests
def test_config_from_env():
    env = {
        "DATABASE_URL": "sqlite:///:memory:",
        "API_KEY": "test-key",
        "DEBUG": "true",
        "TIMEOUT": "60"
    }
    config = AppConfig.from_env(env)
    assert config.debug is True
    assert config.timeout == 60
```

## Step-by-Step Guide: Writing New Tests

### Step 1: Analyze What Needs Testing

1. **Identify the System Under Test (SUT)**
   - What function/class/module are you testing?
   - What are its inputs and outputs?
   - What are its dependencies?

2. **List Test Scenarios**
   - Happy path (normal operation)
   - Edge cases (boundary values)
   - Error cases (invalid inputs)
   - Integration points

3. **Prioritize Scenarios**
   - Critical business logic first
   - High-risk areas second
   - Nice-to-have scenarios last

### Step 2: Choose the Right Test Level

Decision tree for test level selection:

```
Is it testing a single function/method with no I/O?
├─ Yes → Unit Test
└─ No → Does it involve external services/databases?
    ├─ Yes → Does it test the full user journey?
    │   ├─ Yes → E2E Test
    │   └─ No → Integration Test
    └─ No → Unit Test with Mocks
```

### Step 3: Write Test Cases

Follow the Given-When-Then format:

```python
def test_withdraw_sufficient_funds():
    # Given: A bank account with $100 balance
    account = BankAccount(balance=100)
    
    # When: Withdrawing $50
    result = account.withdraw(50)
    
    # Then: The withdrawal succeeds and balance is updated
    assert result.success is True
    assert account.balance == 50
    assert result.transaction_id is not None
```

### Step 4: Implement Test Structure

```python
import pytest
from datetime import datetime, timedelta

class TestUserSubscription:
    """Test suite for user subscription functionality"""
    
    @pytest.fixture
    def active_user(self):
        """Fixture providing an active user with subscription"""
        return User(
            email="test@example.com",
            subscription_end=datetime.utcnow() + timedelta(days=30)
        )
    
    @pytest.fixture
    def expired_user(self):
        """Fixture providing a user with expired subscription"""
        return User(
            email="expired@example.com",
            subscription_end=datetime.utcnow() - timedelta(days=1)
        )
    
    def test_active_subscription_allows_access(self, active_user):
        """Active subscriptions should grant access to premium features"""
        # When
        has_access = active_user.can_access_premium_features()
        
        # Then
        assert has_access is True
    
    def test_expired_subscription_denies_access(self, expired_user):
        """Expired subscriptions should deny access to premium features"""
        # When
        has_access = expired_user.can_access_premium_features()
        
        # Then
        assert has_access is False
    
    @pytest.mark.parametrize("days_until_expiry,expected_warning", [
        (0, True),   # Expires today
        (1, True),   # Expires tomorrow
        (7, True),   # Expires in a week
        (8, False),  # No warning yet
        (30, False), # No warning yet
    ])
    def test_expiration_warning_logic(self, days_until_expiry, expected_warning):
        """Users should see warnings when subscription is expiring soon"""
        # Given
        user = User(
            email="test@example.com",
            subscription_end=datetime.utcnow() + timedelta(days=days_until_expiry)
        )
        
        # When
        should_warn = user.should_show_expiration_warning()
        
        # Then
        assert should_warn == expected_warning
```

### Step 5: Test Naming Conventions

Good test names are descriptive and follow a pattern:

```python
# Pattern: test_<scenario>_<expected_result>
def test_empty_cart_returns_zero_total():
    pass

def test_invalid_email_raises_validation_error():
    pass

def test_concurrent_requests_handle_gracefully():
    pass

# For parameterized tests, use descriptive IDs
@pytest.mark.parametrize("input_value,expected", [
    ("hello", "HELLO"),
    ("Hello World", "HELLO WORLD"),
    ("123", "123"),
    ("", ""),
], ids=["lowercase", "mixed_case", "numbers", "empty_string"])
def test_uppercase_conversion(input_value, expected):
    assert input_value.upper() == expected
```

## Step-by-Step Guide: Evaluating Existing Tests

### Step 1: Test Quality Metrics

Evaluate tests against these criteria:

1. **Readability Score (1-10)**
   - Clear test names?
   - Obvious intent?
   - Well-structured code?

2. **Independence Score (1-10)**
   - No shared state?
   - No execution order dependencies?
   - Proper cleanup?

3. **Coverage Analysis**
   - Line coverage percentage
   - Branch coverage percentage
   - Critical path coverage

4. **Performance Metrics**
   - Execution time
   - Resource usage
   - Parallelization potential

### Step 2: Identify Test Smells

Common test smells and their fixes:

```python
# Smell: Testing implementation details
def test_user_internal_state():  # Bad
    user = User("Alice")
    assert user._internal_cache == {}  # Testing private attribute

# Fix: Test behavior, not implementation
def test_user_cache_behavior():  # Good
    user = User("Alice")
    user.get_profile()  # First call
    user.get_profile()  # Second call should use cache
    assert user.profile_fetch_count == 1  # Test observable behavior

# Smell: Overly complex setup
def test_order_processing():  # Bad
    # 50 lines of setup...
    db.create_tables()
    user = create_user()
    product1 = create_product()
    # ... more setup ...
    
# Fix: Extract to fixtures and helpers
@pytest.fixture
def order_with_items(db_session, user_factory, product_factory):
    user = user_factory()
    products = [product_factory() for _ in range(3)]
    return create_order(user, products)

def test_order_processing(order_with_items):  # Good
    result = process_order(order_with_items)
    assert result.status == "processed"
```

### Step 3: Test Completeness Checklist

- [ ] **Happy Path Coverage**: Normal use cases tested?
- [ ] **Edge Cases**: Boundary values tested?
- [ ] **Error Handling**: Exception scenarios tested?
- [ ] **Negative Tests**: Invalid inputs tested?
- [ ] **Integration Points**: External dependencies tested?
- [ ] **Concurrency**: Thread safety tested (if applicable)?
- [ ] **Performance**: Response time validated?

### Step 4: Refactoring Tests

Guidelines for improving existing tests:

1. **Extract Common Setup**
   ```python
   # Before
   def test_case_1():
       user = User("Alice", "alice@example.com")
       user.activate()
       # ... test logic ...
   
   def test_case_2():
       user = User("Alice", "alice@example.com")
       user.activate()
       # ... test logic ...
   
   # After
   @pytest.fixture
   def active_user():
       user = User("Alice", "alice@example.com")
       user.activate()
       return user
   
   def test_case_1(active_user):
       # ... test logic ...
   
   def test_case_2(active_user):
       # ... test logic ...
   ```

2. **Improve Assertions**
   ```python
   # Before
   assert result == True  # Vague
   
   # After
   assert result is True, "Expected user to be authenticated"
   
   # Even better with custom matchers
   assert_user_authenticated(user)
   ```

3. **Add Missing Test Cases**
   ```python
   # Identify gaps using coverage reports
   # Look for:
   # - Uncovered branches
   # - Error handling paths
   # - Edge cases
   ```

### Step 5: Performance Impact Assessment

```python
import pytest
import time

# Mark slow tests
@pytest.mark.slow
def test_heavy_computation():
    result = expensive_calculation()
    assert result == expected_value

# Add performance benchmarks
def test_api_response_time(benchmark):
    result = benchmark(api_call, "/users")
    assert result.status_code == 200

# Configure test timeouts
@pytest.mark.timeout(5)  # Fails if takes > 5 seconds
def test_with_timeout():
    process_large_dataset()
```

## Supporting Tools and Practices

### Documentation Requirements for Testability

1. **Function/Method Documentation**
   ```python
   def calculate_compound_interest(
       principal: float,
       rate: float,
       time: int,
       frequency: int = 12
   ) -> float:
       """Calculate compound interest.
       
       Args:
           principal: Initial amount (must be positive)
           rate: Annual interest rate as decimal (e.g., 0.05 for 5%)
           time: Time period in years
           frequency: Compounding frequency per year (default: monthly)
       
       Returns:
           Total amount after compound interest
       
       Raises:
           ValueError: If principal <= 0 or rate < 0
       
       Examples:
           >>> calculate_compound_interest(1000, 0.05, 2)
           1104.94
           
       Test Considerations:
           - Test with frequency = 1 (annual), 12 (monthly), 365 (daily)
           - Test edge case where rate = 0
           - Test very large time periods for overflow
       """
   ```

2. **Class-Level Testing Documentation**
   ```python
   class PaymentProcessor:
       """Handles payment processing with various providers.
       
       Testing Strategy:
           - Mock external payment provider calls
           - Test each payment state transition
           - Verify idempotency of operations
           - Test concurrent payment handling
       
       Test Fixtures Available:
           - mock_payment_provider: Returns success responses
           - failing_payment_provider: Simulates failures
           - slow_payment_provider: Simulates timeouts
       """
   ```

### Strategic Code Comments for Test Writers

```python
class OrderService:
    def process_order(self, order: Order) -> ProcessResult:
        # TEST NOTE: This method has side effects:
        # 1. Charges payment (mock payment gateway in tests)
        # 2. Sends email (use email_mock fixture)
        # 3. Updates inventory (use inventory_stub)
        
        if not self.validate_order(order):
            # TEST NOTE: validate_order is a pure function
            # Test separately for better granularity
            raise InvalidOrderError()
        
        # TEST NOTE: The following operations should be atomic
        # Test rollback behavior on failure
        with self.db.transaction():
            payment_result = self.payment_gateway.charge(order.total)
            # TEST NOTE: Test both successful and failed payments
            
            if payment_result.success:
                self.inventory.reserve_items(order.items)
                # TEST NOTE: Test insufficient inventory scenario
                
        return ProcessResult(success=True, transaction_id=payment_result.id)
```

### Test Data Management

1. **Test Data Factories**
   ```python
   # factories.py
   import factory
   from factory import fuzzy
   
   class UserFactory(factory.Factory):
       class Meta:
           model = User
       
       id = factory.Sequence(lambda n: n)
       email = factory.LazyAttribute(lambda obj: f"user{obj.id}@test.com")
       name = factory.Faker('name')
       age = fuzzy.FuzzyInteger(18, 80)
       created_at = factory.LazyFunction(datetime.utcnow)
   
   # Usage in tests
   def test_bulk_user_operations():
       users = UserFactory.create_batch(10)
       result = bulk_update_users(users)
       assert result.updated_count == 10
   ```

2. **Test Data Files**
   ```python
   # conftest.py
   import json
   from pathlib import Path
   
   @pytest.fixture
   def sample_data():
       """Load test data from JSON files"""
       data_dir = Path(__file__).parent / "test_data"
       return {
           "valid_user": json.loads((data_dir / "valid_user.json").read_text()),
           "invalid_users": json.loads((data_dir / "invalid_users.json").read_text()),
           "edge_cases": json.loads((data_dir / "edge_cases.json").read_text()),
       }
   ```

### Continuous Integration Setup

```yaml
# .github/workflows/tests.yml
name: Test Suite

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.10", "3.11", "3.12"]
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: ${{ matrix.python-version }}
    
    - name: Install dependencies
      run: |
        pip install uv
        uv venv
        source .venv/bin/activate
        uv pip install -e ".[test]"
    
    - name: Run linting
      run: |
        source .venv/bin/activate
        ruff check .
        mypy src tests
    
    - name: Run tests with coverage
      run: |
        source .venv/bin/activate
        pytest -v --cov=src --cov-report=xml --cov-report=html
    
    - name: Upload coverage
      uses: codecov/codecov-action@v3
      with:
        file: ./coverage.xml
    
    - name: Run mutation tests
      if: matrix.python-version == '3.11'  # Run only once
      run: |
        source .venv/bin/activate
        mutmut run --paths-to-mutate=src/
        mutmut junitxml > mutmut-results.xml
    
    - name: Upload test results
      if: always()
      uses: actions/upload-artifact@v3
      with:
        name: test-results-${{ matrix.python-version }}
        path: |
          htmlcov/
          mutmut-results.xml
```

### Test Reporting and Metrics

1. **Coverage Reports**
   ```python
   # Generate multiple report formats
   pytest --cov=src \
          --cov-report=term-missing \
          --cov-report=html:htmlcov \
          --cov-report=json:coverage.json \
          --cov-report=xml:coverage.xml
   ```

2. **Custom Test Reports**
   ```python
   # conftest.py
   import json
   from datetime import datetime
   
   def pytest_terminal_summary(terminalreporter, exitstatus, config):
       """Generate custom test summary"""
       stats = terminalreporter.stats
       
       summary = {
           "timestamp": datetime.utcnow().isoformat(),
           "total_tests": len(stats.get('passed', [])) + len(stats.get('failed', [])),
           "passed": len(stats.get('passed', [])),
           "failed": len(stats.get('failed', [])),
           "skipped": len(stats.get('skipped', [])),
           "duration": terminalreporter._sessionstarttime,
       }
       
       with open("test-summary.json", "w") as f:
           json.dump(summary, f, indent=2)
   ```

## Advanced Testing Strategies

### Contract Testing

Test interfaces between services:

```python
import pact

# Consumer test
@pytest.fixture
def pact():
    pact = Consumer('UserService').has_pact_with(Provider('AuthService'))
    pact.start_service()
    yield pact
    pact.stop_service()

def test_user_authentication(pact):
    expected = {
        'id': '123',
        'username': 'testuser',
        'token': pact.Term(r'^[A-Za-z0-9-_]+\.[A-Za-z0-9-_]+\.[A-Za-z0-9-_]+$', 'example.jwt.token')
    }
    
    (pact
     .given('User testuser exists')
     .upon_receiving('a request to authenticate')
     .with_request('POST', '/auth/login', body={'username': 'testuser', 'password': 'secret'})
     .will_respond_with(200, body=expected))
    
    with pact:
        result = authenticate('testuser', 'secret')
        assert result['username'] == 'testuser'
```

### Snapshot Testing

Test complex outputs by comparing to saved snapshots:

```python
import pytest

def test_render_user_profile(snapshot):
    user = User(
        name="Alice Smith",
        email="alice@example.com",
        preferences={"theme": "dark", "notifications": True}
    )
    
    rendered = render_profile_html(user)
    snapshot.assert_match(rendered, 'user_profile.html')

def test_api_response_structure(snapshot):
    response = api_client.get('/api/v1/users/123')
    
    # Normalize dynamic fields before snapshot
    data = response.json()
    data['timestamp'] = 'NORMALIZED_TIMESTAMP'
    data['request_id'] = 'NORMALIZED_REQUEST_ID'
    
    snapshot.assert_match(data, 'user_api_response.json')
```

### Fuzzing

Test with random/malformed inputs:

```python
from hypothesis import given, strategies as st
import atheris

# Property-based fuzzing with Hypothesis
@given(
    username=st.text(min_size=1, max_size=100),
    email=st.emails(),
    age=st.integers(min_value=0, max_value=200)
)
def test_user_creation_with_fuzzing(username, email, age):
    try:
        user = User(username=username, email=email, age=age)
        # If creation succeeds, verify invariants
        assert len(user.username) >= 1
        assert '@' in user.email
        assert 0 <= user.age <= 200
    except ValidationError:
        # Validation errors are expected for invalid inputs
        pass

# Structure-aware fuzzing with atheris
def test_parser_fuzzing():
    @atheris.instrument_func
    def fuzz_target(data):
        try:
            result = parse_configuration(data)
            # If parsing succeeds, verify structure
            assert isinstance(result, dict)
        except ParseError:
            # Parse errors are acceptable
            pass
        except Exception as e:
            # Other exceptions indicate bugs
            raise AssertionError(f"Unexpected exception: {e}")
    
    atheris.Setup(sys.argv, fuzz_target)
    atheris.Fuzz()
```

### Chaos Engineering Principles

Test system resilience:

```python
import chaos

class TestSystemResilience:
    @pytest.mark.slow
    def test_database_failure_handling(self, chaos_monkey):
        """System should handle database failures gracefully"""
        
        # Inject database failure after 5 seconds
        chaos_monkey.schedule_failure(
            target="database",
            failure_type="connection_timeout",
            delay=5
        )
        
        # Start operation that uses database
        future = asyncio.create_task(long_running_operation())
        
        # Operation should complete with degraded functionality
        result = await future
        assert result.status == "completed_with_warnings"
        assert "database unavailable" in result.warnings
    
    def test_memory_pressure(self, memory_limiter):
        """System should handle memory pressure gracefully"""
        
        # Limit available memory to 100MB
        with memory_limiter(100 * 1024 * 1024):
            result = process_large_dataset()
            
            # Should use disk spillover instead of crashing
            assert result.success is True
            assert result.used_disk_spillover is True
```

### Performance Testing Integration

```python
import pytest
from locust import HttpUser, task, between

class TestPerformance:
    @pytest.mark.performance
    def test_api_latency(self, api_client, benchmark):
        """API calls should complete within SLA"""
        
        # Benchmark the API call
        result = benchmark.pedantic(
            api_client.get,
            args=("/api/v1/users",),
            iterations=100,
            rounds=5
        )
        
        # Verify 95th percentile is under 200ms
        stats = benchmark.stats
        assert stats.percentiles[95] < 0.2
    
    @pytest.mark.performance
    def test_bulk_operation_performance(self):
        """Bulk operations should scale linearly"""
        
        sizes = [100, 1000, 10000]
        times = []
        
        for size in sizes:
            data = generate_test_data(size)
            start = time.time()
            process_bulk_data(data)
            times.append(time.time() - start)
        
        # Verify linear scaling (time should roughly 10x for 10x data)
        assert times[1] / times[0] < 12  # Allow 20% overhead
        assert times[2] / times[1] < 12

# Locust load test (save as locustfile.py)
class UserBehavior(HttpUser):
    wait_time = between(1, 3)
    
    @task
    def view_profile(self):
        self.client.get("/profile")
    
    @task(3)  # 3x more likely than view_profile
    def list_items(self):
        self.client.get("/items")
```

## Best Practices Summary

1. **Write Tests First**: Follow TDD when possible
2. **Keep Tests Simple**: Each test should verify one thing
3. **Use Descriptive Names**: Test names should explain what and why
4. **Maintain Test Independence**: Tests should not depend on each other
5. **Mock External Dependencies**: Keep tests fast and reliable
6. **Use Fixtures Wisely**: Share setup, not state
7. **Test Behavior, Not Implementation**: Focus on what, not how
8. **Measure and Monitor**: Track coverage and test execution time
9. **Refactor Tests**: Maintain test code like production code
10. **Document Testing Strategy**: Make it easy for others to contribute

## Conclusion

Effective testing is not about achieving 100% coverage, but about building confidence in your system's behavior. By following these guidelines, you'll create a test suite that:

- Catches bugs before production
- Documents system behavior
- Enables confident refactoring
- Facilitates team collaboration
- Ensures long-term maintainability

Remember: Tests are an investment in your project's future. The time spent writing good tests today saves countless hours of debugging tomorrow.