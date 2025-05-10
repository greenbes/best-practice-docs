# Design Patterns TODO List

## Creational Patterns

1. - [ ] Abstract Factory: Provide an interface for creating families of related or dependent objects without specifying their concrete classes
2. - [ ] Builder: Separate the construction of a complex object from its representation so that the same construction process can create different representations
3. - [ ] Factory Method: Define an interface for creating an object, but let subclasses decide which class to instantiate
4. - [ ] Prototype: Specify the kinds of objects to create using a prototypical instance, and create new objects by copying
this prototype
5. - [ ] Singleton: Ensure a class only has one instance, and provide a global point of access to it

## Structural Patterns

6. - [ ] Adapter: Convert the interface of a class into another interface clients expect
7. - [ ] Bridge: Decouple an abstraction from its implementation so that the two can vary independently
8. - [ ] Composite: Compose objects into tree structures to represent part-whole hierarchies
9. - [ ] Decorator: Attach additional responsibilities to an object dynamically
10. - [ ] Facade: Provide a unified interface to a set of interfaces in a subsystem
11. - [ ] Flyweight: Use sharing to support large numbers of fine-grained objects efficiently
12. - [ ] Proxy: Provide a surrogate or placeholder for another object to control access to it

## Behavioral Patterns

13. - [ ] Chain of Responsibility: Avoid coupling the sender of a request to its receiver by giving more than one object a
chance to handle the request
14. - [ ] Command: Encapsulate a request as an object, thereby letting you parameterize clients with different requests
15. - [ ] Interpreter: Given a language, define a representation for its grammar along with an interpreter that uses the representation to interpret sentences in the language
16. - [ ] Iterator: Provide a way to access the elements of an aggregate object sequentially without exposing its underlying representation
17. - [ ] Mediator: Define an object that encapsulates how a set of objects interact
18. - [ ] Memento: Without violating encapsulation, capture and externalize an object's internal state so that the object can be restored to this state later
19. - [ ] Observer: Define a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically
20. - [ ] State: Allow an object to alter its behavior when its internal state changes. The object will appear to change its class
21. - [ ] Strategy: Define a family of algorithms, encapsulate each one, and make them interchangeable. Strategy lets the algorithm vary independently from clients that use it
22. - [ ] Template Method: Define the skeleton of an algorithm in an operation, deferring some steps to subclasses. Template Method lets subclasses redefine certain steps of an algorithm without changing the algorithm's structure
23. - [ ] Visitor: Represent an operation to be performed on the elements of an object structure. Visitor lets you define a new operation without changing the classes of theelements on which it operates

## Enterprise Integration Patterns

### Integration Styles
24. - [ ] Create a summary and dedicated file for the "File Transfer" pattern.
    - [ ] Read description of File Transfer from https://www.enterpriseintegrationpatterns.com/patterns/messaging/FileTransferIntegration.html
    - [ ] Load the Wikipedia page for the "File Transfer" software pattern.
    - [ ] ADD a list item and short summary of the "File Transfer" pattern to `PATTERN_LIST.md` 
25. - [ ] Read description of Shared Database from https://www.enterpriseintegrationpatterns.com/patterns/messaging/SharedDataBaseIntegration.html
26. - [ ] Read description of Remote Procedure Invocation from https://www.enterpriseintegrationpatterns.com/patterns/messaging/EncapsulatedSynchronousIntegration.html
27. - [ ] Read description of Messaging from https://www.enterpriseintegrationpatterns.com/patterns/messaging/Messaging.html

### Messaging Channels
28. - [ ] Read description of Message Channel from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageChannel.html
29. - [ ] Read description of Point-to-Point Channel from https://www.enterpriseintegrationpatterns.com/patterns/messaging/PointToPointChannel.html
30. - [ ] Read description of Publish-Subscribe Channel from https://www.enterpriseintegrationpatterns.com/patterns/messaging/PublishSubscribeChannel.html
31. - [ ] Read description of Datatype Channel from https://www.enterpriseintegrationpatterns.com/patterns/messaging/DatatypeChannel.html
32. - [ ] Read description of Invalid Message Channel from https://www.enterpriseintegrationpatterns.com/patterns/messaging/InvalidMessageChannel.html
33. - [ ] Read description of Dead Letter Channel from https://www.enterpriseintegrationpatterns.com/patterns/messaging/DeadLetterChannel.html
34. - [ ] Read description of Guaranteed Delivery from https://www.enterpriseintegrationpatterns.com/patterns/messaging/GuaranteedMessaging.html
35. - [ ] Read description of Channel Adapter from https://www.enterpriseintegrationpatterns.com/patterns/messaging/ChannelAdapter.html
36. - [ ] Read description of Messaging Bridge from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessagingBridge.html
37. - [ ] Read description of Message Bus from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageBus.html

### Message Construction
38. - [ ] Read description of Message from https://www.enterpriseintegrationpatterns.com/patterns/messaging/Message.html
39. - [ ] Read description of Command Message from https://www.enterpriseintegrationpatterns.com/patterns/messaging/CommandMessage.html
40. - [ ] Read description of Document Message from https://www.enterpriseintegrationpatterns.com/patterns/messaging/DocumentMessage.html
41. - [ ] Read description of Event Message from https://www.enterpriseintegrationpatterns.com/patterns/messaging/EventMessage.html
42. - [ ] Read description of Request-Reply from https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html
43. - [ ] Read description of Return Address from https://www.enterpriseintegrationpatterns.com/patterns/messaging/ReturnAddress.html
44. - [ ] Read description of Correlation Identifier from https://www.enterpriseintegrationpatterns.com/patterns/messaging/CorrelationIdentifier.html
45. - [ ] Read description of Message Sequence from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageSequence.html
46. - [ ] Read description of Message Expiration from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageExpiration.html
47. - [ ] Read description of Format Indicator from https://www.enterpriseintegrationpatterns.com/patterns/messaging/FormatIndicator.html

### Message Routing
48. - [ ] Read description of Pipes-and-Filters from https://www.enterpriseintegrationpatterns.com/patterns/messaging/PipesAndFilters.html
49. - [ ] Read description of Message Router from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageRouter.html
50. - [ ] Read description of Content-based Router from https://www.enterpriseintegrationpatterns.com/patterns/messaging/ContentBasedRouter.html
51. - [ ] Read description of Message Filter from https://www.enterpriseintegrationpatterns.com/patterns/messaging/Filter.html
52. - [ ] Read description of Dynamic Router from https://www.enterpriseintegrationpatterns.com/patterns/messaging/DynamicRouter.html
53. - [ ] Read description of Recipient List from https://www.enterpriseintegrationpatterns.com/patterns/messaging/RecipientList.html
54. - [ ] Read description of Splitter from https://www.enterpriseintegrationpatterns.com/patterns/messaging/Sequencer.html
55. - [ ] Read description of Aggregator from https://www.enterpriseintegrationpatterns.com/patterns/messaging/Aggregator.html
56. - [ ] Read description of Resequencer from https://www.enterpriseintegrationpatterns.com/patterns/messaging/Resequencer.html
57. - [ ] Read description of Composed Msg. Processor from https://www.enterpriseintegrationpatterns.com/patterns/messaging/DistributionAggregate.html
58. - [ ] Read description of Scatter-Gather from https://www.enterpriseintegrationpatterns.com/patterns/messaging/BroadcastAggregate.html
59. - [ ] Read description of Routing Slip from https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html
60. - [ ] Read description of Process Manager from https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html
61. - [ ] Read description of Message Broker from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageBroker.html

### Message Transformation
62. - [ ] Read description of Message Translator from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageTranslator.html
63. - [ ] Read description of Envelope Wrapper from https://www.enterpriseintegrationpatterns.com/patterns/messaging/EnvelopeWrapper.html
64. - [ ] Read description of Content Enricher from https://www.enterpriseintegrationpatterns.com/patterns/messaging/DataEnricher.html
65. - [ ] Read description of Content Filter from https://www.enterpriseintegrationpatterns.com/patterns/messaging/ContentFilter.html
66. - [ ] Read description of Claim Check from https://www.enterpriseintegrationpatterns.com/patterns/messaging/StoreInLibrary.html
67. - [ ] Read description of Normalizer from https://www.enterpriseintegrationpatterns.com/patterns/messaging/Normalizer.html
68. - [ ] Read description of Canonical Data Model from https://www.enterpriseintegrationpatterns.com/patterns/messaging/CanonicalDataModel.html

### Messaging Endpoints
69. - [ ] Read description of Message Endpoint from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageEndpoint.html
70. - [ ] Read description of Messaging Gateway from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessagingGateway.html
71. - [ ] Read description of Messaging Mapper from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessagingMapper.html
72. - [ ] Read description of Transactional Client from https://www.enterpriseintegrationpatterns.com/patterns/messaging/TransactionalClient.html
73. - [ ] Read description of Polling Consumer from https://www.enterpriseintegrationpatterns.com/patterns/messaging/PollingConsumer.html
74. - [ ] Read description of Event-driven Consumer from https://www.enterpriseintegrationpatterns.com/patterns/messaging/EventDrivenConsumer.html
75. - [ ] Read description of Competing Consumers from https://www.enterpriseintegrationpatterns.com/patterns/messaging/CompetingConsumers.html
76. - [ ] Read description of Message Dispatcher from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageDispatcher.html
77. - [ ] Read description of Selective Consumer from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageSelector.html
78. - [ ] Read description of Durable Subscriber from https://www.enterpriseintegrationpatterns.com/patterns/messaging/DurableSubscription.html
79. - [ ] Read description of Idempotent Receiver from https://www.enterpriseintegrationpatterns.com/patterns/messaging/IdempotentReceiver.html
80. - [ ] Read description of Service Activator from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessagingAdapter.html

### System Management
81. - [ ] Read description of Control Bus from https://www.enterpriseintegrationpatterns.com/patterns/messaging/ControlBus.html
82. - [ ] Read description of Detour from https://www.enterpriseintegrationpatterns.com/patterns/messaging/Detour.html
83. - [ ] Read description of Wire Tap from https://www.enterpriseintegrationpatterns.com/patterns/messaging/WireTap.html
84. - [ ] Read description of Message History from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageHistory.html
85. - [ ] Read description of Message Store from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageStore.html
86. - [ ] Read description of Smart Proxy from https://www.enterpriseintegrationpatterns.com/patterns/messaging/SmartProxy.html
87. - [ ] Read description of Test Message from https://www.enterpriseintegrationpatterns.com/patterns/messaging/TestMessage.html
88. - [ ] Read description of Channel Purger from https://www.enterpriseintegrationpatterns.com/patterns/messaging/ChannelPurger.html

