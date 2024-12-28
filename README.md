# Statechart Engine User Guide

## Table of Contents
- [Introduction](#introduction)
- [Core Concepts](#core-concepts)
- [Creating Statecharts](#creating-statecharts)
- [State Types](#state-types)
- [Transitions](#transitions)
- [Actions and Guards](#actions-and-guards)
- [Services](#services)
- [Testing](#testing)
- [Best Practices](#best-practices)
- [Examples](#examples)

## Introduction

The Statechart Engine is a TypeScript-based implementation of the UML Statechart specification. It provides type-safe state management with sophisticated features like parallel states, history states, and asynchronous invoked services.

UML State Machines are a powerful tool for modeling the dynamic behavior of systems. They provide a visual representation of states and transitions, making it easier to understand complex workflows. The type-state pattern, on the other hand, is a programming technique that leverages type systems to enforce state constraints at compile time, reducing runtime errors and improving code safety. Together, these concepts form the foundation of robust state management in software design.

### Key Features
- Full TypeScript support
- SCXML compliance
- Service Registry
- Parallel state execution
- History states
- Service invocation
- Comprehensive testing tools
- Built-in monitoring
- State machines operate asynchronously
- Export states in `plantuml` format for visualization

## UML Statecharts

### Semantics of UML Statecharts

UML Statecharts define the behavior of a system in terms of states, transitions, events, and actions. Each state represents a condition or situation during the life of an object, and transitions define the movement from one state to another, triggered by events. Actions can be associated with transitions, states, or events, allowing for complex behavior modeling.

Key semantics include:

- **States**: Represent the status of an object at a particular time.
- **Transitions**: Define the change from one state to another, triggered by events.
- **Events**: External or internal occurrences that trigger transitions.
- **Actions**: Operations executed in response to events or during transitions.

Statecharts are particularly useful in scenarios where systems have multiple states and complex transitions, such as user interfaces, protocol handling, and control systems. By using statecharts, developers can design systems that are more robust, maintainable, and easier to understand.

## Core Concepts

### State Machine Configuration
A state machine is defined by a configuration object with the following structure:

```typescript
interface StateMachineConfig<TState extends { type: string; context: any; events: any }> {
  initial: TState['type'];
  states: Record<TState['type'], State<TState['context'], TState['events'], TState['type']>>;
}
```

### Context
Context is the data model of your state machine. It holds the data that can be modified by actions and accessed by guards:

```typescript
interface PaymentContext {
  amount: number;
  error: string | null;
}
```

### Events
Events are objects with a `type` property that trigger transitions:

```typescript
type PaymentEvent = {
  [S in PaymentState as string]: {
    [E in keyof S['events']]: {
      type: E;
    } & S['events'][E];
  }
}[string];
```

## Creating Statecharts

### Basic Structure

```typescript
const paymentMachine: StateMachineConfig<PaymentState> = {
  initial: 'idle',
  context: {
    amount: 0,
    paymentMethod: null,
    error: null
  },
  states: {
    idle: {
      on: {
        START_PAYMENT: {
          target: 'processing',
          guard: (context, event) => event.amount > 0,
          actions: [(context, event) => {
            context.amount = event.amount;
          }],
        }
      }
    },
    // ... other states
  }
};
```

### Using the Machine

```typescript
function PaymentFlow() {
  const [state, send] = useStateMachine(paymentMachine);

  return (
    <div>
      <button onClick={() => send({ type: 'START_PAYMENT', amount: 100 })}>
        Pay
      </button>
    </div>
  );
}
```

## State Types

### Atomic States
Simple states with no substates:

```typescript
{
  processing: {
    on: {
      PAYMENT_SUCCESS: 'success',
      PAYMENT_ERROR: 'error'
    }
  }
}
```

### Compound States
States containing substates:

```typescript
{
  payment: {
    initial: 'validating',
    states: {
      validating: {
        on: {
          VALID: 'processing',
          INVALID: 'error'
        }
      },
      processing: {
        // ...
      }
    }
  }
}
```

### Parallel States
States with concurrent regions:

```typescript
{
  form: {
    type: 'parallel',
    regions: {
      validation: {
        initial: 'pending',
        states: {
          pending: { /* ... */ },
          valid: { /* ... */ },
          invalid: { /* ... */ }
        }
      },
      submission: {
        initial: 'idle',
        states: {
          idle: { /* ... */ },
          submitting: { /* ... */ }
        }
      }
    }
  }
}
```

### History States
States that remember their last active substate:

```typescript
{
  checkout: {
    initial: 'cart',
    states: {
      cart: { /* ... */ },
      payment: { /* ... */ },
      confirmation: { /* ... */ },
      hist: {
        type: 'history',
        historyType: 'deep'
      }
    }
  }
}
```

## Transitions

### Basic Transitions

```typescript
{
  idle: {
    on: {
      START: 'processing'
    }
  }
}
```

### Transitions with Guards

```typescript
{
  idle: {
    on: {
      START: {
        target: 'processing',
        guard: (context) => context.amount > 0
      }
    }
  }
}
```

### Transitions with Actions

```typescript
{
  idle: {
    on: {
      START: {
        target: 'processing',
        actions: [
          (context, event) => {
            context.startTime = Date.now();
          }
        ]
      }
    }
  }
}
```

### Internal Transitions
Transitions that don't exit the current state:

```typescript
{
  processing: {
    on: {
      UPDATE: {
        actions: [(context, event) => {
          context.progress = event.progress;
        }]
      }
    }
  }
}
```

## Actions and Guards

### Entry Actions

```typescript
{
  processing: {
    onEntry: [
      (context) => {
        context.startTime = Date.now();
      }
    ],
    // ...
  }
}
```

### Exit Actions

```typescript
{
  processing: {
    onExit: [
      (context) => {
        context.endTime = Date.now();
      }
    ],
    // ...
  }
}
```

### Guards

```typescript
{
  idle: {
    on: {
      SUBMIT: [
        {
          target: 'processing',
          guard: (context) => context.isValid,
          actions: [(context) => { /* ... */ }]
        },
        {
          target: 'error',
          guard: (context) => !context.isValid
        }
      ]
    }
  }
}
```

## Services

### Basic Service Invocation

```typescript
{
  processing: {
    invoke: [
      {
        id: 'paymentService',
        src: async (context) => {
          const response = await processPayment(context.amount);
          return response.data;
        },
        onDone: {
          target: 'success',
          actions: [(context, event) => {
            context.confirmation = event.data;
          }]
        },
        onError: {
          target: 'error',
          actions: [(context, event) => {
            context.error = event.data;
          }]
        }
      }
    ]
  }
}
```

### Service Discovery

```typescript
{
  processing: {
    invoke: [
      {
        id: 'payment',
        src: 'paymentService',
        onDone: 'success',
        onError: 'error'
      }
    ]
  }
}
```

## Testing

### Basic Test Case

```typescript
const tester = new StatechartTester(paymentMachine);

test('successful payment flow', async () => {
  const result = await tester.runTest({
    name: 'Successful payment',
    events: [
      { type: 'START_PAYMENT', amount: 100 },
      { type: 'CONFIRM_PAYMENT' }
    ],
    assertions: [
      state => state.matches('success'),
      state => state.context.amount === 100
    ]
  });

  expect(result.success).toBe(true);
});
```

### Coverage Testing

```typescript
test('minimum coverage requirements', async () => {
  const { coverage } = await tester.runTestSuite([
    // ... test cases
  ]);

  expect(coverage).toHaveMinimumCoverage({
    states: 90,
    transitions: 85,
    actions: 80
  });
});
```

## Best Practices

### 1. State Organization
- Keep states atomic and focused
- Use hierarchy to manage complexity
- Group related states in compounds
- Use parallel states for independent concerns

### 2. Context Management
- Keep context minimal
- Use TypeScript interfaces
- Initialize all context properties
- Update context immutably

### 3. Event Design
- Use descriptive event names
- Keep event payloads minimal
- Use discriminated unions for type safety

### 4. Error Handling
- Always handle error states
- Provide recovery paths
- Log errors appropriately
- Use proper error context

### 5. Testing
- Test all possible paths
- Verify state transitions
- Check context updates
- Test error scenarios
- Maintain high coverage

## Examples

### Form Handling

```typescript
const formMachine = {
  id: 'form',
  initial: 'idle',
  context: {
    data: {},
    errors: {}
  },
  states: {
    idle: {
      on: {
        SUBMIT: {
          target: 'validating',
          actions: [(context, event) => {
            context.data = event.data;
          }]
        }
      }
    },
    validating: {
      invoke: [
        {
          src: async (context) => validateForm(context.data),
          onDone: 'submitting',
          onError: {
            target: 'error',
            actions: [(context, event) => {
              context.errors = event.data;
            }]
          }
        }
      ]
    },
    submitting: {
      invoke: [
        {
          src: async (context) => submitForm(context.data),
          onDone: 'success',
          onError: 'error'
        }
      ]
    },
    success: {
      type: 'final'
    },
    error: {
      on: {
        RETRY: 'validating'
      }
    }
  }
};
```

### Authentication Flow

```typescript
const authMachine = {
  id: 'auth',
  initial: 'checking',
  context: {
    user: null,
    error: null
  },
  states: {
    checking: {
      invoke: [
        {
          src: 'checkAuth',
          onDone: {
            target: 'authenticated',
            actions: [(context, event) => {
              context.user = event.data;
            }]
          },
          onError: 'unauthenticated'
        }
      ]
    },
    authenticated: {
      on: {
        LOGOUT: {
          target: 'unauthenticated',
          actions: [(context) => {
            context.user = null;
          }]
        }
      }
    },
    unauthenticated: {
      on: {
        LOGIN: {
          target: 'authenticating'
        }
      }
    },
    authenticating: {
      invoke: [
        {
          src: 'login',
          onDone: {
            target: 'authenticated',
            actions: [(context, event) => {
              context.user = event.data;
            }]
          },
          onError: {
            target: 'unauthenticated',
            actions: [(context, event) => {
              context.error = event.data;
            }]
          }
        }
      ]
    }
  }
};
```
# Advanced State Machine Documentation

## Table of Contents
- [Advanced API Examples](#advanced-api-examples)
- [Advanced State Patterns](#advanced-state-patterns)
- [Performance Optimization](#performance-optimization)
- [Security Guidelines](#security-guidelines)
- [Deployment Guide](#deployment-guide)

## Advanced API Examples

### Complex State Management

#### Parallel State Processing
```typescript
const orderMachine = {
  id: 'order',
  initial: 'processing',
  states: {
    processing: {
      type: 'parallel',
      regions: {
        payment: {
          initial: 'validating',
          states: {
            validating: {
              invoke: {
                src: 'validatePayment',
                onDone: 'authorized',
                onError: 'failed'
              }
            },
            authorized: { type: 'final' },
            failed: {}
          }
        },
        fulfillment: {
          initial: 'checking',
          states: {
            checking: {
              invoke: {
                src: 'checkInventory',
                onDone: 'ready',
                onError: 'backorder'
              }
            },
            ready: { type: 'final' },
            backorder: {}
          }
        }
      }
    }
  }
};
```

#### History States
```typescript
const checkoutMachine = {
  id: 'checkout',
  initial: 'cart',
  states: {
    cart: {
      on: { NEXT: 'delivery' }
    },
    delivery: {
      on: { 
        NEXT: 'payment',
        BACK: 'cart'
      }
    },
    payment: {
      on: { 
        NEXT: 'review',
        BACK: 'delivery'
      }
    },
    review: {
      on: { 
        BACK: 'history',
        CONFIRM: 'confirmed'
      }
    },
    history: {
      type: 'history',
      history: 'deep'
    },
    confirmed: { type: 'final' }
  }
};
```

### Advanced Event Handling

#### Event Debouncing
```typescript
function useDebouncedEvent(sendEvent, delay = 500) {
  return useCallback(
    debounce((event) => sendEvent(event), delay),
    [sendEvent]
  );
}

// Usage
const debouncedSend = useDebouncedEvent(sendEvent);
debouncedSend({ type: 'UPDATE_SEARCH', query: searchTerm });
```

#### Event Batching
```typescript
const batchProcessor = {
  initial: 'collecting',
  states: {
    collecting: {
      after: {
        5000: 'processing'
      },
      on: {
        ADD_ITEM: {
          actions: 'collectItem'
        }
      }
    },
    processing: {
      invoke: {
        src: 'processBatch',
        onDone: 'collecting'
      }
    }
  }
};
```

### Complex Service Integration

#### Saga Pattern
```typescript
const orderSagaMachine = {
  id: 'orderSaga',
  initial: 'initiating',
  context: {
    compensations: []
  },
  states: {
    initiating: {
      invoke: {
        src: 'createOrder',
        onDone: {
          target: 'processingPayment',
          actions: ['recordCompensation']
        },
        onError: 'failed'
      }
    },
    processingPayment: {
      invoke: {
        src: 'processPayment',
        onDone: {
          target: 'reservingInventory',
          actions: ['recordCompensation']
        },
        onError: 'compensating'
      }
    },
    reservingInventory: {
      invoke: {
        src: 'reserveInventory',
        onDone: 'completed',
        onError: 'compensating'
      }
    },
    compensating: {
      invoke: {
        src: 'executeCompensations',
        onDone: 'failed'
      }
    },
    completed: { type: 'final' },
    failed: { type: 'final' }
  }
};
```

## Advanced State Patterns

### Actor Model Pattern
```typescript
interface ActorRef {
  send: (event: any) => void;
  subscribe: (callback: (state: any) => void) => () => void;
}

class StateMachineActor implements ActorRef {
  private subscribers = new Set<(state: any) => void>();
  private machine: StateMachineInstance;

  constructor(config: StateMachineConfig) {
    this.machine = new StateMachineInstance(config);
  }

  send(event: any) {
    const nextState = this.machine.transition(event);
    this.notify(nextState);
  }

  subscribe(callback: (state: any) => void) {
    this.subscribers.add(callback);
    return () => this.subscribers.delete(callback);
  }

  private notify(state: any) {
    this.subscribers.forEach(callback => callback(state));
  }
}
```

### State Machine Composition
```typescript
interface SubMachineConfig {
  machineId: string;
  context: any;
}

const compositeMachine = {
  id: 'parent',
  context: {
    subMachines: new Map<string, SubMachineConfig>()
  },
  states: {
    active: {
      invoke: {
        src: 'subMachineCoordinator',
        data: (context) => ({
          machines: context.subMachines
        })
      },
      on: {
        'SUB_MACHINE.DONE': {
          actions: ['updateSubMachineState']
        }
      }
    }
  }
};
```

### State Machine Patterns

#### Circuit Breaker
```typescript
const circuitBreakerMachine = {
  id: 'circuitBreaker',
  initial: 'closed',
  context: {
    failures: 0,
    threshold: 5,
    resetTimeout: 60000
  },
  states: {
    closed: {
      on: {
        FAILURE: {
          actions: 'incrementFailures',
          target: 'closed',
          cond: 'belowThreshold'
        },
        FAILURE_THRESHOLD: 'open'
      }
    },
    open: {
      after: {
        RESET_TIMEOUT: 'halfOpen'
      }
    },
    halfOpen: {
      on: {
        SUCCESS: 'closed',
        FAILURE: 'open'
      }
    }
  }
};
```

#### Retry Pattern
```typescript
const retryMachine = {
  id: 'retry',
  initial: 'idle',
  context: {
    retries: 0,
    maxRetries: 3,
    backoff: 1000
  },
  states: {
    idle: {
      on: { START: 'attempting' }
    },
    attempting: {
      invoke: {
        src: 'operation',
        onDone: 'success',
        onError: [
          {
            target: 'waiting',
            cond: 'canRetry'
          },
          { target: 'failure' }
        ]
      }
    },
    waiting: {
      after: {
        BACKOFF: {
          target: 'attempting',
          actions: 'incrementRetry'
        }
      }
    },
    success: { type: 'final' },
    failure: { type: 'final' }
  }
};
```

## Performance Optimization

### State Machine Optimization

#### Context Immutability
```typescript
function optimizedReducer(state, event) {
  // Use immer for immutable updates
  return produce(state, draft => {
    switch (event.type) {
      case 'UPDATE':
        draft.value = event.data;
        break;
    }
  });
}
```

#### Event Batching
```typescript
class BatchingStateMachine {
  private eventQueue: Event[] = [];
  private batchTimeout: NodeJS.Timeout | null = null;

  queueEvent(event: Event) {
    this.eventQueue.push(event);
    this.scheduleBatchProcessing();
  }

  private scheduleBatchProcessing() {
    if (!this.batchTimeout) {
      this.batchTimeout = setTimeout(() => {
        this.processBatch();
      }, 16); // ~1 frame
    }
  }

  private processBatch() {
    const events = this.eventQueue;
    this.eventQueue = [];
    this.batchTimeout = null;
    
    // Process events in batch
    let state = this.currentState;
    for (const event of events) {
      state = this.transition(state, event);
    }
    this.updateState(state);
  }
}
```

### WebSocket Optimization

#### Connection Pooling
```typescript
class WebSocketPool {
  private pool: Map<string, WebSocket> = new Map();
  private maxSize: number = 10;

  async getConnection(machineId: string): Promise<WebSocket> {
    // Reuse existing connection if available
    if (this.pool.has(machineId)) {
      return this.pool.get(machineId)!;
    }

    // Create new connection if pool not full
    if (this.pool.size < this.maxSize) {
      const ws = await this.createConnection();
      this.pool.set(machineId, ws);
      return ws;
    }

    // Wait for available connection
    return this.waitForConnection();
  }

  private async waitForConnection(): Promise<WebSocket> {
    return new Promise((resolve) => {
      const checkInterval = setInterval(() => {
        if (this.pool.size < this.maxSize) {
          clearInterval(checkInterval);
          resolve(this.getConnection(''));
        }
      }, 100);
    });
  }
}
```

#### Message Compression
```typescript
class CompressedWebSocketClient extends WebSocketClient {
  async sendEvent(machineId: string, event: any) {
    const compressed = await compressMessage({ machineId, event });
    return this.send(compressed);
  }

  protected async handleMessage(message: ArrayBuffer) {
    const decompressed = await decompressMessage(message);
    super.handleMessage(decompressed);
  }
}
```

### Persistence Optimization

#### Caching
```typescript
class CachedStateMachine {
  private cache: Map<string, {
    state: any;
    timestamp: number;
    ttl: number;
  }> = new Map();

  async getState(machineId: string): Promise<any> {
    const cached = this.cache.get(machineId);
    if (cached && Date.now() - cached.timestamp < cached.ttl) {
      return cached.state;
    }

    const state = await this.loadState(machineId);
    this.cache.set(machineId, {
      state,
      timestamp: Date.now(),
      ttl: 5000
    });
    return state;
  }
}
```

#### Batch Persistence
```typescript
class BatchPersistence {
  private batch: Map<string, any> = new Map();
  private batchTimeout: NodeJS.Timeout | null = null;

  saveState(machineId: string, state: any) {
    this.batch.set(machineId, state);
    this.scheduleBatch();
  }

  private scheduleBatch() {
    if (!this.batchTimeout) {
      this.batchTimeout = setTimeout(() => {
        this.persistBatch();
      }, 1000);
    }
  }

  private async persistBatch() {
    const states = Array.from(this.batch.entries());
    this.batch.clear();
    this.batchTimeout = null;
    
    await this.persistence.bulkSave(states);
  }
}
```

## Security Guidelines

### Authentication and Authorization

#### JWT Authentication
```typescript
class SecureStateMachineService {
  validateToken(token: string): boolean {
    try {
      const decoded = jwt.verify(token, process.env.JWT_SECRET!);
      return true;
    } catch {
      return false;
    }
  }

  @Authorized(['admin', 'operator'])
  async createMachine(machineId: string, config: any) {
    // Create machine
  }

  @Authorized(['admin', 'operator', 'viewer'])
  async getState(machineId: string) {
    // Get state
  }
}
```

#### Permission Management
```typescript
interface StateMachinePermissions {
  read: string[];
  write: string[];
  admin: string[];
}

class PermissionManager {
  private permissions: Map<string, StateMachinePermissions> = new Map();

  async checkPermission(
    userId: string,
    machineId: string,
    action: 'read' | 'write' | 'admin'
  ): Promise<boolean> {
    const machinePerms = this.permissions.get(machineId);
    if (!machinePerms) return false;

    return machinePerms[action].includes(userId);
  }
}
```

### Data Security

#### Encryption
```typescript
class EncryptedPersistence implements PersistenceAdapter {
  private readonly key: Buffer;

  constructor(encryptionKey: string) {
    this.key = Buffer.from(encryptionKey, 'base64');
  }

  async saveState(machineId: string, state: any): Promise<void> {
    const encrypted = await this.encrypt(state);
    await this.persistence.save(machineId, encrypted);
  }

  async loadState(machineId: string): Promise<any> {
    const encrypted = await this.persistence.load(machineId);
    if (!encrypted) return null;
    return this.decrypt(encrypted);
  }

  private async encrypt(data: any): Promise<Buffer> {
    const iv = crypto.randomBytes(16);
    const cipher = crypto.createCipheriv('aes-256-gcm', this.key, iv);
    const encrypted = Buffer.concat([
      cipher.update(JSON.stringify(data), 'utf8'),
      cipher.final()
    ]);
    const authTag = cipher.getAuthTag();
    return Buffer.concat([iv, authTag, encrypted]);
  }

  private async decrypt(data: Buffer): Promise<any> {
    const iv = data.slice(0, 16);
    const authTag = data.slice(16, 32);
    const encrypted = data.slice(32);
    const decipher = crypto.createDecipheriv('aes-256-gcm', this.key, iv);
    decipher.setAuthTag(authTag);
    const decrypted = Buffer.concat([
      decipher.update(encrypted),
      decipher.final()
    ]);
    return JSON.parse(decrypted.toString('utf8'));
  }
}
```

