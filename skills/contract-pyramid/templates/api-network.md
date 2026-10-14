<!-- destination: `.context/contracts/02-API-NETWORK-CONTRACT.md` — do not copy this comment into the target -->

# API and network

## Envelope

Success uses `success`, `data`, and `meta` when the response is a page. Error uses `success: false` and `error.code`, `error.message`, `error.details`.

`GET /healthz` is outside the envelope.

## Status mapping

The code strings and status numbers are the table in `.context/02-SHARED-CONTRACTS/error-codes.md`. Handlers do not invent a code.

## JSON

- Transport field names are camelCase.
- A list with no rows is `[]`.
- Create returns 201. A successful command that returns the aggregate returns 200. Delete returns 200 and a message.
