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

#### Audit Logging
```typescript
interface AuditLog {
  timestamp: number;
  userId: string;
  action: string;
  machineId: string;
  event?: any;
  state?: any;
}

class AuditLogger {
  async logEvent(log: AuditLog): Promise<void> {
    await this.persistence.saveAuditLog({
      ...log,
      timestamp: Date.now()
    });
  }

  async