Broadway Bundle
===============

Symfony bundle to integrate Broadway into your Symfony application.

## Installation

### Symfony Flex

The easiest way to install and configure the BroadwayBundle with Symfony is by using
[Symfony Flex](https://github.com/symfony/flex).

Make sure you have Symfony Flex installed:

```
$ composer require symfony/flex ^1.0
$ composer config extra.symfony.allow-contrib true
```

Install the bundle:

```
$ composer require broadway/broadway-bundle
```

Symfony Flex will automatically register and configure the bundle.

By default in-memory implementations of the event store and read models are used.
You can install one of the persistent implementations as described in the
`Event store` and `Read model` sections below.

### Manually

Install the bundle:

```
$ composer require broadway/broadway-bundle
```

Register the bundle in your application kernel:

```
$bundles = array(
    // ..
    new Broadway\Bundle\BroadwayBundle\BroadwayBundle(),
);

```

## Event store

Broadway provides several event store implementations.
By default the [InMemoryEventStore](https://github.com/broadway/broadway/blob/master/src/Broadway/EventStore/InMemoryEventStore.php) is
used.

There a several optional persisting event store implementations:
* [broadway/event-store-dbal](https://github.com/broadway/event-store-dbal) using [Doctrine DBAL](https://github.com/doctrine/dbal)
* [broadway/event-store-mongodb](https://github.com/broadway/event-store-mongodb) using MongoDB

These can be very easily installed using [Symfony Flex](https://github.com/symfony/flex).

Of course there are other implementations and you can also create your own:

```yaml
# config.yml
broadway:
  # a service definition id implementing Broadway\EventStore\EventStore,
  event_store: "my_event_store"
```

@ todo move to event-store-dbal
To generate the MySQL schema for the event store use the following command:

```bash
bin/console broadway:event-store:schema:init
```

The schema can be dropped using

```bash
bin/console broadway:event-store:schema:drop
```

## Read models

Broadway provides several read model implementations.
By default the [in memory](https://github.com/broadway/broadway/tree/master/src/Broadway/ReadModel/InMemory) 
read model implementation is used.

There a several optional persisting read model implementations:
* [broadway/read-model-elasticsearch](https://github.com/broadway/read-model-elasticsearch) using Elasticsearch
* [broadway/read-model-mongodb](https://github.com/broadway/read-model-mongodb) using MongoDB

These can be very easily installed using [Symfony Flex](https://github.com/symfony/flex).

Of course there are other implementations and you can also create your own:

```yaml
# config.yml
broadway:
  # a service definition id implementing Broadway\ReadModel\RepositoryFactory
  read_model: "my_read_model_repository_factory"
```

## Services

Once enabled the bundle will expose several services, such as:

- `broadway.command_handling.command_bus` command bus to inject if you use commands
- `broadway.event_store` alias to the active event store
- `broadway.uuid.generator` active uuid generator

## Tags

The bundle provides several tags to use in your service configuration.

### Command handler

Register command handler using `broadway.command_handler` service tag:
```xml
<service class="TestCommandHandler">
    <tag name="broadway.command_handler" />
</service>
```

### Domain event listeners

Register listeners (such as projectors) that respond and act on domain events:

```xml
<tag name="broadway.domain.event_listener" />
```

### Event listeners

For example an event listener that collects successfully executed commands:

```xml
<tag name="broadway.event_listener"
    event="broadway.command_handling.command_success"
    method="onCommandHandlingSuccess" />
```

## Metadata enrichers

It is possible to add additional metadata to persisted events. This is useful
for recording extra contextual (auditing) data such as the currently logged in
user, an ip address or some request token.

```xml
<tag name="broadway.metadata_enricher" />
```

### Sagas

Broadway provides a saga implementation using `MongoDB`
in [broadway/broadway-saga](https://github.com/broadway/broadway-saga).

This can be installed using composer:

```
$ composer require broadway/broadway-saga
```

When using [Symfony Flex](https://github.com/symfony/flex) the required
services are configured automatically.

Register sagas using the `broadway.saga` service tag:
 
```xml
<!-- services.xml -->
<service class="ReservationSaga">
    <argument type="service" id="broadway.command_handling.command_bus" />
    <argument type="service" id="broadway.uuid.generator" />
    <tag name="broadway.saga" type="reservation" />
</service>
```

## Configuration

There are some basic configuration options available at this point. The
options are mostly targeted on providing different setups based on production
or testing usage.

```yml
# config.yml
broadway:
    event_store:          ~ # a service definition id implementing Broadway\EventStore\EventStore, by default the broadway.event_store.in_memory will be used
    read_model:           ~ # a service definition id implementing Broadway\ReadModel\RepositoryFactory, by default the broadway.read_model.in_memory.repository_factory will be used
    serializer:
        payload:          ~ # default: broadway.simple_interface_serializer
        readmodel:        ~ # default: broadway.simple_interface_serializer
        metadata:         ~ # default: broadway.simple_interface_serializer
    command_handling:
        logger:           false # If you want to log every command handled, provide the logger's service id here (e.g. "logger")
    saga:
        enabled:          ~ # default: false 
        state_repository: ~ # a service definition id implementing Broadway\Saga\State\RepositoryInterface, by default the broadway.saga.state.in_memory_repository will be used
```
