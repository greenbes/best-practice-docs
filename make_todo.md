# Prompt: Convert Specification to Detailed TODO List

You are a software architect tasked with converting a technical specification into a detailed, actionable TODO list. The TODO list must be complete and ordered such that another LLM can follow it step-by-step to implement the entire specification.

## Input

You will receive a technical specification document describing a feature or system to be built.

## Output Requirements

Create a TODO list that:
1. Can be executed in order without circular dependencies
2. References specific sections of the specification for implementation details
3. Includes explicit file paths and function/class names
4. Contains concrete acceptance criteria for each task
5. Groups related tasks into logical milestones

## Instructions

### Step 1: Analyze the Specification

First, identify and list:
- All new files that need to be created
- All existing files that need to be modified
- All configuration changes required
- All external dependencies (libraries, services, APIs)

### Step 2: Create Dependency Graph

Map out the dependencies between components:
1. List each file/module
2. Draw arrows showing what depends on what
3. Identify the critical path (what must be done first)
4. Mark tasks that can be done in parallel

### Step 3: Generate Tasks

For each file or component, create tasks following this pattern:

**For new files:**
```
- [ ] Create `path/to/filename.py` with class/function stubs
  - Spec reference: Section X.Y
  - Create: ClassName with methods: method1(), method2()
  - Dependencies: None
  - Acceptance: File exists with correct structure
```

**For modifications:**
```
- [ ] Add feature_name to `path/to/existing.py`
  - Spec reference: Section X.Y
  - Add: new_method() to ExistingClass
  - Dependencies: [previous task ID]
  - Acceptance: Method exists and passes type checking
```

**For configurations:**
```
- [ ] Add config_section to configuration schema
  - Spec reference: Section X.Y showing config format
  - Add: Fields x, y, z with types and defaults
  - Dependencies: Config model exists
  - Acceptance: Config validates example from spec
```

### Step 4: Add Supporting Tasks

For each component, include:

**Testing tasks:**
```
- [ ] Write unit tests for ComponentName
  - Test: Happy path for method1()
  - Test: Error cases for method2()
  - Test: Edge cases from spec section X.Y
  - Acceptance: All tests pass, coverage > 90%
```

**Error handling tasks:**
```
- [ ] Add error handling for feature_name
  - Add: CustomException class
  - Handle: Specific error cases from spec
  - Acceptance: Errors caught and logged properly
```

**Integration tasks:**
```
- [ ] Wire up component_a to component_b
  - Connect: Output of A to input of B
  - Add: Error handling for connection failures
  - Acceptance: Data flows correctly between components
```

### Step 5: Structure the Output

Organize tasks into this format:

```markdown
# TODO: [Project/Feature Name]

## Prerequisites
- [ ] Install library_name==version
- [ ] Set up SERVICE_NAME with credentials
- [ ] Ensure Python >= X.Y

## Milestone 1: Foundation
*Goal: Basic data structures and configuration*

### Task 1.1: Create Data Models
- [ ] Create `src/models.py` with data structures
  - Spec reference: Section 2.1 "Data Models"
  - Create: ModelA, ModelB classes from spec examples
  - Dependencies: None
  - Acceptance: Models instantiate with spec examples

### Task 1.2: Update Configuration
- [ ] Add new_feature section to `src/config.py`
  - Spec reference: Section 2.2 "Configuration Format"
  - Add: Configuration class with validation
  - Dependencies: Task 1.1 (uses ModelA)
  - Acceptance: Config loads spec example correctly

## Milestone 2: Core Implementation
*Goal: Main feature functionality*

### Task 2.1: Create Manager Class
- [ ] Create `src/manager.py` with ManagerClass
  - Spec reference: Section 3 "Implementation Details"
  - Methods: process(), validate(), execute()
  - Dependencies: Tasks 1.1, 1.2
  - Acceptance: Manager processes spec examples

[Continue with all tasks...]

## Milestone N: Documentation and Polish
*Goal: User-ready with docs and examples*

### Task N.1: Update Documentation
- [ ] Add usage examples to README
  - Examples: All examples from spec section 4
  - Dependencies: All implementation complete
  - Acceptance: User can follow examples successfully
```

### Task ID Format

Use hierarchical IDs: `[Milestone].[Task]` (e.g., 1.1, 1.2, 2.1)

### Critical Requirements

1. **No forward references**: A task can only depend on tasks that appear earlier
2. **Explicit paths**: Always use full file paths from project root
3. **Concrete names**: Use actual class/function names, not placeholders
4. **Spec references**: Every task must reference the specification section it implements
5. **Testable criteria**: Each task must have specific acceptance criteria

### Example Task (Complete)

```
### Task 2.3: Add API endpoint for feature_x
- [ ] Add `/api/feature_x` endpoint to `src/api/routes.py`
  - Spec reference: Section 3.2 "API Endpoints"
  - Add: post_feature_x() function
  - Parameters: data: FeatureXRequest (from models.py)
  - Returns: FeatureXResponse
  - Dependencies: Task 1.1 (models), Task 2.1 (manager)
  - Implementation:
    1. Import FeatureXManager from src/manager.py
    2. Validate request using FeatureXRequest model
    3. Call manager.process(data)
    4. Return FeatureXResponse
  - Acceptance: 
    - Endpoint responds at /api/feature_x
    - Accepts JSON matching spec example
    - Returns expected response format
    - Handles errors with 400/500 status codes
```

## Your Task

Given the specification provided, create a complete TODO list following the format above. Ensure that:
1. Every line of code that needs to be written has a corresponding task
2. Tasks are ordered so dependencies are satisfied
3. The specification is fully implemented when all tasks are complete
4. Another LLM could follow this TODO list without seeing the original spec (though they can reference it)