# DEN Deduplicated in-app notification outbox

Private service package, version 0.1.0, JSON contract version `1.0.0`. Plain ESM with no third-party runtime dependencies. Backend-compatible exports from `src/index.js`: `enqueue, inbox, markRead`.

Run `npm test` and `npm run check` on Node 24. CI runs both. Tests cover successful state transitions and authorization/idempotency failure paths; they do not establish production readiness.

## Integration boundary

Only trusted server business events enqueue recipient IDs; public clients must never select arbitrary recipients. Persist deduplication key with UNIQUE constraint in the same business transaction. Inbox and markRead always scope to authenticated active user. Notification resource access must be checked again on click.

State transitions return new state; callers must store results transactionally with compare-and-swap/unique constraints. In-memory arrays are command fixtures, not production storage. The parent backend vendors a pinned copy plus checksum manifest for private-repository builds; this package is the authoritative source. Client-provided actors, statuses, receipt flags and balances have no authority.

## Current readiness

In-app domain state works locally. No external email/SMS/push provider, delivery worker, retry/dead-letter implementation or consumer endpoint until backend integrates it.

No external resources are provisioned, no credentials are present, and no live money is moved. See `contract.json` for the version envelope; domain payload validation is exported by the functions above.
