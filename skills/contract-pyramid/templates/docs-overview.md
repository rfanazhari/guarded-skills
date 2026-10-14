<!-- destination: `docs/01-system-overview.md` — do not copy this comment into the target -->

# {{Service}} system overview

{{Service}} is a small HTTP service for the `{{Aggregate}}` aggregate. It is shaped so a prototype can run with an in-memory store and the same port can be served by a durable store later.

## Capabilities

- Create, read, list, and delete a `{{Aggregate}}`.
- Move a `{{Aggregate}}` through the transitions listed in the behavioral contract.
- Return one JSON envelope for success and one for errors.
- Expose `GET /healthz` outside that envelope.

## Shape

```
clients
  HTTP JSON
    delivery
      application use cases
        domain entities and ports
          infrastructure adapters
```

The composition root wires adapters and the server. It contains no business rules.
