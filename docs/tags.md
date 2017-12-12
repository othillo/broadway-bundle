# Tags

The bundle provides several tags to use in your service configuration.

## Command handler

Register command handler using `broadway.command_handler` service tag:
```xml
<service class="TestCommandHandler">
    <tag name="broadway.command_handler" />
</service>
```

## Domain event listeners

Register listeners (such as projectors) that respond and act on domain events:

```xml
<tag name="broadway.domain.event_listener" />
```

## Event listeners

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
