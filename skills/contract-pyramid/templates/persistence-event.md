<!-- destination: `.context/contracts/03-PERSISTENCE-EVENT-CONTRACT.md` — do not copy this comment into the target -->

# Persistence

## Rules

- The application generates the id before insert.
- `created_at` and `updated_at` come from the clock injected into the use case.
- Insert of an id that already exists returns the conflict sentinel in both adapters.
- Read and delete of an unknown id return the not-found sentinel in both adapters.
- Delete for `{{Aggregate}}` follows `docs/04-database-design.md`. The default is hard delete: the record is gone, and no `deleted_at` column exists.
- A list page past the end returns an empty collection and the true total.

## Schema

The column list in `docs/04-database-design.md` is the schema. The durable adapter may create that schema on startup. That startup step is not a versioned migration.

## Events

This service has no outbound event contract. Add one here before publishing a message.
