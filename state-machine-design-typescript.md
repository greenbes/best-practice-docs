# State Machine Design Guide

## Table of Contents
1. [Principles of State Machine Design](#principles-of-state-machine-design)
2. [Problem Decomposition](#problem-decomposition)
3. [Design Patterns](#design-patterns)
4. [Common Pitfalls](#common-pitfalls)
5. [Examples](#examples)

## Principles of State Machine Design

## Best Practices

1. State Design
    - Keep states atomic and focused
    - Use compound states for complex flows
    - Handle error states explicitly
    - Define clear entry/exit actions

2. Event Handling
    - Use type-safe events
    - Include necessary payload data
    - Handle timeouts appropriately
    - Validate event data

3. Context Management
    - Keep context minimal
    - Use immutable updates
    - Include validation
    - Document context requirements

4. Error Handling
    - Define error states
    - Provide recovery paths
    - Log error details
    - Include error context

5. Testing
    - Test all state transitions
    - Verify error handling
    - Test concurrent operations
    - Validate context updates

### Core Principles

#### Single Responsibility

    - Each state should represent one clear situation
    - States should not mix concerns
    - Each transition should have one clear trigger

   ```typescript
   // Bad: Mixed concerns
   {
     "processing": {
       on: {
         "PAYMENT_RECEIVED": "shipping",
         "UPDATE_CUSTOMER": { /* handling customer updates */ }
       }
     }
   }

   // Good: Separated concerns
   {
     "payment": {
       on: {
         "PAYMENT_RECEIVED": "preparing_shipment"
       }
     },
     "customer_profile": {
       on: {
         "UPDATE_CUSTOMER": { /* customer updates */ }
       }
     }
   }
   ```

#### State Cohesion
    - Group related states together
    - Use compound states for related behaviors
    - Share common transitions at parent level

   ```typescript
   // Good: Cohesive compound state
   {
     "payment_processing": {
       initial: "validating",
       states: {
         "validating": {
           on: { "VALID": "authorizing" }
         },
         "authorizing": {
           on: { "AUTHORIZED": "capturing" }
         },
         "capturing": {
           on: { "CAPTURED": "#success" }
         }
       },
       on: {
         "ERROR": "#error" // Shared error handling
       }
     }
   }
   ```

 #### Explicit State Names
    - Use descriptive, action-oriented names
    - Avoid ambiguous states
    - Name states based on business domain

   ```typescript
   // Bad: Ambiguous states
   {
     "step1": { /* ... */ },
     "step2": { /* ... */ },
     "done": { /* ... */ }
   }

   // Good: Descriptive states
   {
     "collecting_shipping_info": { /* ... */ },
     "validating_address": { /* ... */ },
     "address_confirmed": { /* ... */ }
   }
   ```

### Context Design

#### Minimal Context
    - Keep only necessary data
    - Use derived state where possible
    - Clear ownership of data

   ```typescript
   // Bad: Redundant context
   interface OrderContext {
     items: OrderItem[];
     itemCount: number;      // Derived
     totalPrice: number;     // Derived
     hasItems: boolean;      // Derived
     shippingAddress: Address;
     billingAddress: Address;
     sameAsShipping: boolean; // Redundant
   }

   // Good: Minimal context
   interface OrderContext {
     items: OrderItem[];
     shippingAddress: Address;
     billingAddress?: Address; // Optional, uses shipping if not set
   }
   ```

#### Immutable Updates
    - Use immutable patterns
    - Clear update points
    - Traceable changes

   ```typescript
   // Good: Immutable context updates
   const machine = createMachine({
     context: {
       items: [] as OrderItem[]
     },
     states: {
       collecting: {
         on: {
           ADD_ITEM: {
             actions: assign({
               items: (context, event) => [...context.items, event.item]
             })
           }
         }
       }
     }
   });
   ```

## Problem Decomposition

### Analysis Steps

#### Identify States
    - List all possible situations
    - Group related situations
    - Identify final states

   ```typescript
   // Example: Order Processing
   enum OrderStates {
     // Collection Phase
     CartEmpty,
     CollectingItems,
     
     // Validation Phase
     ValidatingInventory,
     ConfirmingAvailability,
     
     // Payment Phase
     AwaitingPayment,
     ProcessingPayment,
     
     // Fulfillment Phase
     PreparingShipment,
     Shipped,
     
     // Final States
     Completed,
     Cancelled
   }
   ```

#### Define Events
    - List all triggers
    - Group by source
    - Identify shared events

   ```typescript
   // Example: Order Events
   type OrderEvent =
     // User Events
     | { type: 'ADD_ITEM'; item: Item }
     | { type: 'REMOVE_ITEM'; itemId: string }
     | { type: 'SUBMIT_PAYMENT'; payment: PaymentInfo }
     
     // System Events
     | { type: 'INVENTORY_CHECKED' }
     | { type: 'PAYMENT_PROCESSED' }
     
     // Error Events
     | { type: 'INVENTORY_ERROR'; reason: string }
     | { type: 'PAYMENT_ERROR'; code: string }
     
     // Timeout Events
     | { type: 'PAYMENT_TIMEOUT' }
     | { type: 'SESSION_EXPIRED' };
   ```

#### Identify Parallel Processes
    - Look for independent concerns
    - Identify synchronization points
    - Define communication patterns

   ```typescript
   const orderMachine = createMachine({
     type: 'parallel',
     states: {
       inventory: {
         initial: 'checking',
         states: {
           checking: { /* ... */ },
           confirmed: { /* ... */ },
           failed: { /* ... */ }
         }
       },
       payment: {
         initial: 'awaiting',
         states: {
           awaiting: { /* ... */ },
           processing: { /* ... */ },
           completed: { /* ... */ }
         }
       },
       shipping: {
         initial: 'collecting',
         states: {
           collecting: { /* ... */ },
           validating: { /* ... */ },
           ready: { /* ... */ }
         }
       }
     }
   });
   ```

### Decomposition Strategies

#### Hierarchical Decomposition
    - Break down complex flows into sub-machines
    - Use compound states for encapsulation
    - Share common behaviors at parent level

   ```typescript
   const checkoutMachine = createMachine({
     initial: 'shipping',
     states: {
       shipping: {
         initial: 'collecting',
         states: {
           collecting: { /* ... */ },
           validating: { /* ... */ }
         },
         on: {
           BACK: 'cart',     // Shared navigation
           CANCEL: 'cancelled' // Shared cancellation
         }
       },
       payment: {
         initial: 'method_selection',
         states: {
           method_selection: { /* ... */ },
           processing: { /* ... */ }
         },
         on: {
           BACK: 'shipping',
           CANCEL: 'cancelled'
         }
       }
     }
   });
   ```

#### Service-Based Decomposition
    - Separate long-running operations
    - Define clear interfaces
    - Handle timeouts and errors

   ```typescript
   const paymentMachine = createMachine({
     states: {
       processing: {
         invoke: {
           src: 'processPayment',
           onDone: 'success',
           onError: 'failure',
           data: {
             timeout: 5000,
             retries: 3
           }
         }
       }
     }
   });
   ```

## Design Patterns

### State Management Patterns

#### Guard Pattern
    - Use guards for conditional transitions
    - Keep guards pure and simple
    - Document guard conditions

   ```typescript
   const machine = createMachine({
     states: {
       validating: {
         on: {
           SUBMIT: [
             {
               target: 'processing',
               guard: 'hasValidAmount',
               actions: 'logValidSubmission'
             },
             {
               target: 'error',
               actions: 'logValidationError'
             }
           ]
         }
       }
     }
   });
   ```

#### History Pattern
    - Use for complex navigation
    - Preserve user context
    - Clear history when appropriate

   ```typescript
   const formMachine = createMachine({
     states: {
       form: {
         initial: 'step1',
         states: {
           step1: { /* ... */ },
           step2: { /* ... */ },
           step3: { /* ... */ },
           hist: {
             type: 'history',
             history: 'deep'
           }
         },
         on: {
           SAVE: '.hist'  // Return to last position
         }
       }
     }
   });
   ```

#### Saga Pattern
    - Coordinate multiple services
    - Handle compensating actions
    - Maintain transaction log

   ```typescript
   const orderSaga = createMachine({
     context: {
       compensations: [] as Array<() => Promise<void>>
     },
     states: {
       creating_order: {
         invoke: {
           src: 'createOrder',
           onDone: {
             target: 'processing_payment',
             actions: 'recordCompensation'
           }
         }
       },
       processing_payment: {
         invoke: {
           src: 'processPayment',
           onDone: 'success',
           onError: 'compensating'
         }
       },
       compensating: {
         invoke: {
           src: 'executeCompensations',
           onDone: 'failed'
         }
       }
     }
   });
   ```

### Error Handling Patterns

#### Retry Pattern
    - Implement exponential backoff
    - Track retry counts
    - Handle permanent failures

   ```typescript
   const serviceMachine = createMachine({
     context: {
       retries: 0,
       maxRetries: 3
     },
     states: {
       processing: {
         invoke: {
           src: 'someService',
           onError: 'retrying'
         }
       },
       retrying: {
         entry: assign({ retries: (ctx) => ctx.retries + 1 }),
         after: {
           // Exponential backoff
           RETRY_DELAY: [
             {
               target: 'processing',
               guard: (ctx) => ctx.retries < ctx.maxRetries
             },
             { target: 'failure' }
           ]
         }
       }
     }
   });
   ```

#### Error Recovery Flow
    - Implement compensating actions
    - Maintain error context
    - Support manual intervention

   ```typescript
   const recoveryMachine = createMachine({
     context: {
       error: null,
       recoverySteps: []
     },
     states: {
       error: {
         on: {
           RETRY: {
             target: 'recovering',
             actions: 'prepareRecoverySteps'
           },
           MANUAL_RESOLVE: 'manual_intervention'
         }
       },
       recovering: {
         invoke: {
           src: 'executeRecovery',
           onDone: 'operational',
           onError: 'error'
         }
       },
       manual_intervention: {
         on: {
           RESOLVED: 'operational',
           FAILED: 'error'
         }
       }
     }
   });
   ```

#### Timeout Handling
    - Define timeout boundaries
    - Implement cleanup actions
    - Support graceful degradation

   ```typescript
   const timeoutMachine = createMachine({
     states: {
       active: {
         after: {
           5000: {
             target: 'timeout',
             actions: 'cleanup'
           }
         },
         invoke: {
           src: 'longRunningOperation',
           onDone: 'success'
         }
       },
       timeout: {
         entry: 'notifyTimeout',
         on: {
           RETRY: 'active',
           FALLBACK: 'degraded'
         }
       },
       degraded: {
         // Fallback behavior
       }
     }
   });
   ```

### Common Pitfalls

#### State Explosion
    - Too many direct transitions
    - Insufficient use of compound states
    - Missing shared behaviors

   ```typescript
   // Bad: State explosion
   {
     "idle": {
       on: {
         "START": "validating",
         "ERROR": "error",
         "CANCEL": "cancelled"
       }
     },
     "validating": {
       on: {
         "VALID": "processing",
         "ERROR": "error",
         "CANCEL": "cancelled"
       }
     },
     "processing": {
       on: {
         "DONE": "success",
         "ERROR": "error",
         "CANCEL": "cancelled"
       }
     }
   }

   // Good: Using compound states
   {
     "active": {
       on: {
         "ERROR": "error",
         "CANCEL": "cancelled"
       },
       states: {
         "idle": {
           on: { "START": "validating" }
         },
         "validating": {
           on: { "VALID": "processing" }
         },
         "processing": {
           on: { "DONE": "success" }
         }
       }
     }
   }
   ```

#### Context Bloat
    - Storing derived data
    - Missing data normalization
    - Unclear ownership

#### Unclear State Boundaries
    - Ambiguous state names
    - Mixed responsibilities
    - Missing documentation

## Examples

### E-commerce Checkout Flow

```typescript
const checkoutMachine = createMachine({
  id: 'checkout',
  initial: 'cart',
  context: {
    items: [] as CartItem[],
    shipping: null as Address | null,
    payment: null as PaymentMethod | null
  },
  states: {
    cart: {
      on: {
        ADD_ITEM: {
          actions: 'addItemToCart'
        },
        REMOVE_ITEM: {
          actions: 'removeItemFromCart'
        },
        CHECKOUT: {
          target: 'shipping',
          guard: 'hasItems'
        }
      }
    },
    shipping: {
      initial: 'collecting',
      states: {
        collecting: {
          on: {
            SUBMIT_ADDRESS: {
              target: 'validating',
              actions: 'saveAddress'
            }
          }
        },
        validating: {
          invoke: {
            src: 'validateAddress',
            onDone: 'validated',
            onError: 'collecting'
          }
        },
        validated: {
          type: 'final'
        }
      },
      onDone: 'payment'
    },
    payment: {
      initial: 'selecting',
      states: {
        selecting: {
          on: {
            SELECT_METHOD: {
              target: 'processing',
              actions: 'savePaymentMethod'
            }
          }
        },
        processing: {
          invoke: {
            src: 'processPayment',
            onDone: 'completed',
            onError: 'selecting'
          }
        },
        completed: {
          type: 'final'
        }
      },
      onDone: 'confirmation'
    },
    confirmation: {
      type: 'final'
    }
  }
});
```

### Form Wizard

```typescript
const formWizard = createMachine({
  id: 'wizard',
  initial: 'form',
  context: {
    data: {} as Record<string, any>,
    errors: {} as Record<string, string>
  },
  states: {
    form: {
      initial: 'personal',
      states: {
        personal: {
          on: {
            NEXT: {
              target: 'contact',
              guard: 'isPersonalValid'
            },
            UPDATE: {
              actions: 'updateData'
            }
          }
        },
        contact: {
          on: {
            NEXT: {
              target: 'review',
              guard: 'isContactValid'
            },
            BACK: 'personal',
            UPDATE: {
              actions: 'updateData'
            }
          }
        },
        review: {
          on: {
            SUBMIT: '#wizard.submitting',
            BACK: 'contact',
            EDIT_PERSONAL: 'personal',
            EDIT_CONTACT: 'contact'
          }
        },
        hist: {
          type: 'history',
          history: 'deep'
        }
      }
    },
    submitting: {
      invoke: {
        src: 'submitForm',
        onDone: 'success',
        onError: 'form.hist'
      }
    },
    success: {
      type: 'final'
    }
  }
});
```

These examples demonstrate:
- Clear state organization
- Proper context management
- Error handling
- Navigation patterns
- Service integration
- Type safety
- Event handling
- Guard conditions

### State Machine Composition

1. **Actor Model Integration**
    - Define clear boundaries
    - Use message passing
    - Handle actor lifecycle

   ```typescript
   const parentMachine = createMachine({
     invoke: {
       id: 'childActor',
       src: childMachine,
       data: {
         // Context mapping
         initialData: (context) => ({
           userId: context.userId
         })
       },
       onDone: 'success',
       onError: 'error'
     },
     on: {
       CHILD_EVENT: {
         actions: sendTo('childActor', { type: 'PARENT_RESPONSE' })
       }
     }
   });
   ```

2. **Machine Communication**
    - Define clear interfaces
    - Use typed events
    - Handle synchronization

   ```typescript
   interface MachineA {
     events: {
       type: 'SYNC' | 'UPDATE';
       data?: any;
     };
   }

   interface MachineB {
     events: {
       type: 'ACKNOWLEDGE' | 'REJECT';
       reason?: string;
     };
   }

   const systemMachine = createMachine({
     type: 'parallel',
     states: {
       machineA: {
         invoke: {
           id: 'serviceA',
           src: machineAService
         }
       },
       machineB: {
         invoke: {
           id: 'serviceB',
           src: machineBService
         }
       },
       coordinator: {
         on: {
           'serviceA.SYNC': {
             actions: sendTo('serviceB', { type: 'SYNC' })
           },
           'serviceB.ACKNOWLEDGE': {
             actions: sendTo('serviceA', { type: 'PROCEED' })
           }
         }
       }
     }
   });
   ```

### Testing Strategies

1. **Unit Testing**
   ```typescript
   describe('Payment Machine', () => {
     const machine = createMachine({/*...*/});
     
     it('should transition to processing on SUBMIT', () => {
       const state = machine.transition('idle', { type: 'SUBMIT' });
       expect(state.matches('processing')).toBe(true);
     });

     it('should maintain context during transitions', () => {
       const state = machine.transition('idle', { 
         type: 'UPDATE', 
         data: { amount: 100 } 
       });
       expect(state.context.amount).toBe(100);
     });
   });
   ```

2. **Integration Testing**
   ```typescript
   test('complete checkout flow', async () => {
     const checkoutService = interpret(checkoutMachine)
       .onTransition((state) => {
         // Assert state invariants
       })
       .start();

     await checkoutService.send({ type: 'ADD_ITEM', item });
     expect(checkoutService.state.context.items).toHaveLength(1);

     await checkoutService.send({ type: 'CHECKOUT' });
     expect(checkoutService.state.matches('payment')).toBe(true);
   });
   ```


