---
"@routedock/routedock": minor
---

Harden settlement and diagnostics across the SDK.

- The x402 providers (Hono, Express, Fastify) now treat a settle result as successful only when it returns `success: true` with a non-empty transaction hash. A facilitator failure (`{ success: false, errorReason }`) or an empty transaction now returns 402 with the payment requirements header instead of serving the resource, writing the idempotency record, or firing `onSettled`.
- `RouteDockClient.pay()` reserves against the local daily and per-endpoint spend caps on the nulth vault path too. Vault payments previously returned before `_checkAndReserveSpend()`, bypassing `spendCap` entirely and leaving vault spend out of the accumulator; they now commit on success and roll back on failure, including mode/prover validation errors.
- `@routedock/routedock/testing` callback types are derived from the real `RouteDockMiddlewareOptions`, so they now carry the `payer` argument and the harness settles `mpp-session-ws` as a session (open, vouchers, close) reporting the transport as `mode`. `SyntheticPayment` gains `payer`, defaulting to a valid Stellar account and honoring an explicit `null`.
- `RouteDockLogger` is now `(level, message, fields?)` and provider adapters accept a `logger` option, so every internal diagnostic can be silenced, redirected, or structured instead of going straight to `console`. The default remains console-backed.
