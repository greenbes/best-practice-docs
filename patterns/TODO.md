# Design Patterns TODO List

## Creational Patterns

- [ ] Abstract Factory: Provide an interface for creating families of related or dependent objects without specifying their concrete classes
- [ ] Builder: Separate the construction of a complex object from its representation so that the same construction process can create different representations
- [ ] Factory Method: Define an interface for creating an object, but let subclasses decide which class to instantiate
- [ ] Prototype: Specify the kinds of objects to create using a prototypical instance, and create new objects by copying
this prototype
- [ ] Singleton: Ensure a class only has one instance, and provide a global point of access to it

## Structural Patterns

- [ ] Adapter: Convert the interface of a class into another interface clients expect
- [ ] Bridge: Decouple an abstraction from its implementation so that the two can vary independently
- [ ] Composite: Compose objects into tree structures to represent part-whole hierarchies
- [ ] Decorator: Attach additional responsibilities to an object dynamically
- [ ] Facade: Provide a unified interface to a set of interfaces in a subsystem
- [ ] Flyweight: Use sharing to support large numbers of fine-grained objects efficiently
- [ ] Proxy: Provide a surrogate or placeholder for another object to control access to it

## Behavioral Patterns

- [ ] Chain of Responsibility: Avoid coupling the sender of a request to its receiver by giving more than one object a
chance to handle the request
- [ ] Command: Encapsulate a request as an object, thereby letting you parameterize clients with different requests
- [ ] Interpreter: Given a language, define a representation for its grammar along with an interpreter that uses the representation to interpret sentences in the language
- [ ] Iterator: Provide a way to access the elements of an aggregate object sequentially without exposing its underlying representation
- [ ] Mediator: Define an object that encapsulates how a set of objects interact
- [ ] Memento: Without violating encapsulation, capture and externalize an object's internal state so that the object can be restored to this state later
- [ ] Observer: Define a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically
- [ ] State: Allow an object to alter its behavior when its internal state changes. The object will appear to change its class
- [ ] Strategy: Define a family of algorithms, encapsulate each one, and make them interchangeable. Strategy lets the algorithm vary independently from clients that use it
- [ ] Template Method: Define the skeleton of an algorithm in an operation, deferring some steps to subclasses. Template Method lets subclasses redefine certain steps of an algorithm without changing the algorithm's structure
- [ ] Visitor: Represent an operation to be performed on the elements of an object structure. Visitor lets you define a new operation without changing the classes of theelements on which it operates

## Enterprise Integration Patterns

### Integration Styles
- [ ] Read description of File Transfer from https://www.enterpriseintegrationpatterns.com/patterns/messaging/FileTransferIntegration.html
- [ ] Read description of Shared Database from https://www.enterpriseintegrationpatterns.com/patterns/messaging/SharedDataBaseIntegration.html
- [ ] Read description of Remote Procedure Invocation from https://www.enterpriseintegrationpatterns.com/patterns/messaging/EncapsulatedSynchronousIntegration.html
- [ ] Read description of Messaging from https://www.enterpriseintegrationpatterns.com/patterns/messaging/Messaging.html

### Messaging Channels
- [ ] Read description of Message Channel from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageChannel.html
- [ ] Read description of Point-to-Point Channel from https://www.enterpriseintegrationpatterns.com/patterns/messaging/PointToPointChannel.html
- [ ] Read description of Publish-Subscribe Channel from https://www.enterpriseintegrationpatterns.com/patterns/messaging/PublishSubscribeChannel.html
- [ ] Read description of Datatype Channel from https://www.enterpriseintegrationpatterns.com/patterns/messaging/DatatypeChannel.html
- [ ] Read description of Invalid Message Channel from https://www.enterpriseintegrationpatterns.com/patterns/messaging/InvalidMessageChannel.html
- [ ] Read description of Dead Letter Channel from https://www.enterpriseintegrationpatterns.com/patterns/messaging/DeadLetterChannel.html
- [ ] Read description of Guaranteed Delivery from https://www.enterpriseintegrationpatterns.com/patterns/messaging/GuaranteedMessaging.html
- [ ] Read description of Channel Adapter from https://www.enterpriseintegrationpatterns.com/patterns/messaging/ChannelAdapter.html
- [ ] Read description of Messaging Bridge from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessagingBridge.html
- [ ] Read description of Message Bus from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageBus.html

### Message Construction
- [ ] Read description of Message from https://www.enterpriseintegrationpatterns.com/patterns/messaging/Message.html
- [ ] Read description of Command Message from https://www.enterpriseintegrationpatterns.com/patterns/messaging/CommandMessage.html
- [ ] Read description of Document Message from https://www.enterpriseintegrationpatterns.com/patterns/messaging/DocumentMessage.html
- [ ] Read description of Event Message from https://www.enterpriseintegrationpatterns.com/patterns/messaging/EventMessage.html
- [ ] Read description of Request-Reply from https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html
- [ ] Read description of Return Address from https://www.enterpriseintegrationpatterns.com/patterns/messaging/ReturnAddress.html
- [ ] Read description of Correlation Identifier from https://www.enterpriseintegrationpatterns.com/patterns/messaging/CorrelationIdentifier.html
- [ ] Read description of Message Sequence from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageSequence.html
- [ ] Read description of Message Expiration from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageExpiration.html
- [ ] Read description of Format Indicator from https://www.enterpriseintegrationpatterns.com/patterns/messaging/FormatIndicator.html

### Message Routing
- [ ] Read description of Pipes-and-Filters from https://www.enterpriseintegrationpatterns.com/patterns/messaging/PipesAndFilters.html
- [ ] Read description of Message Router from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageRouter.html
- [ ] Read description of Content-based Router from https://www.enterpriseintegrationpatterns.com/patterns/messaging/ContentBasedRouter.html
- [ ] Read description of Message Filter from https://www.enterpriseintegrationpatterns.com/patterns/messaging/Filter.html
- [ ] Read description of Dynamic Router from https://www.enterpriseintegrationpatterns.com/patterns/messaging/DynamicRouter.html
- [ ] Read description of Recipient List from https://www.enterpriseintegrationpatterns.com/patterns/messaging/RecipientList.html
- [ ] Read description of Splitter from https://www.enterpriseintegrationpatterns.com/patterns/messaging/Sequencer.html
- [ ] Read description of Aggregator from https://www.enterpriseintegrationpatterns.com/patterns/messaging/Aggregator.html
- [ ] Read description of Resequencer from https://www.enterpriseintegrationpatterns.com/patterns/messaging/Resequencer.html
- [ ] Read description of Composed Msg. Processor from https://www.enterpriseintegrationpatterns.com/patterns/messaging/DistributionAggregate.html
- [ ] Read description of Scatter-Gather from https://www.enterpriseintegrationpatterns.com/patterns/messaging/BroadcastAggregate.html
- [ ] Read description of Routing Slip from https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html
- [ ] Read description of Process Manager from https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html
- [ ] Read description of Message Broker from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageBroker.html

### Message Transformation
- [ ] Read description of Message Translator from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageTranslator.html
- [ ] Read description of Envelope Wrapper from https://www.enterpriseintegrationpatterns.com/patterns/messaging/EnvelopeWrapper.html
- [ ] Read description of Content Enricher from https://www.enterpriseintegrationpatterns.com/patterns/messaging/DataEnricher.html
- [ ] Read description of Content Filter from https://www.enterpriseintegrationpatterns.com/patterns/messaging/ContentFilter.html
- [ ] Read description of Claim Check from https://www.enterpriseintegrationpatterns.com/patterns/messaging/StoreInLibrary.html
- [ ] Read description of Normalizer from https://www.enterpriseintegrationpatterns.com/patterns/messaging/Normalizer.html
- [ ] Read description of Canonical Data Model from https://www.enterpriseintegrationpatterns.com/patterns/messaging/CanonicalDataModel.html

### Messaging Endpoints
- [ ] Read description of Message Endpoint from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageEndpoint.html
- [ ] Read description of Messaging Gateway from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessagingGateway.html
- [ ] Read description of Messaging Mapper from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessagingMapper.html
- [ ] Read description of Transactional Client from https://www.enterpriseintegrationpatterns.com/patterns/messaging/TransactionalClient.html
- [ ] Read description of Polling Consumer from https://www.enterpriseintegrationpatterns.com/patterns/messaging/PollingConsumer.html
- [ ] Read description of Event-driven Consumer from https://www.enterpriseintegrationpatterns.com/patterns/messaging/EventDrivenConsumer.html
- [ ] Read description of Competing Consumers from https://www.enterpriseintegrationpatterns.com/patterns/messaging/CompetingConsumers.html
- [ ] Read description of Message Dispatcher from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageDispatcher.html
- [ ] Read description of Selective Consumer from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageSelector.html
- [ ] Read description of Durable Subscriber from https://www.enterpriseintegrationpatterns.com/patterns/messaging/DurableSubscription.html
- [ ] Read description of Idempotent Receiver from https://www.enterpriseintegrationpatterns.com/patterns/messaging/IdempotentReceiver.html
- [ ] Read description of Service Activator from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessagingAdapter.html

### System Management
- [ ] Read description of Control Bus from https://www.enterpriseintegrationpatterns.com/patterns/messaging/ControlBus.html
- [ ] Read description of Detour from https://www.enterpriseintegrationpatterns.com/patterns/messaging/Detour.html
- [ ] Read description of Wire Tap from https://www.enterpriseintegrationpatterns.com/patterns/messaging/WireTap.html
- [ ] Read description of Message History from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageHistory.html
- [ ] Read description of Message Store from https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageStore.html
- [ ] Read description of Smart Proxy from https://www.enterpriseintegrationpatterns.com/patterns/messaging/SmartProxy.html
- [ ] Read description of Test Message from https://www.enterpriseintegrationpatterns.com/patterns/messaging/TestMessage.html
- [ ] Read description of Channel Purger from https://www.enterpriseintegrationpatterns.com/patterns/messaging/ChannelPurger.html

