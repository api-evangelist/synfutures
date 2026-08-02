---
name: Place a SynFutures RWA order via One Click
description: Enable One Click (1CT) delegation and place or cancel a tokenized-stock order on SynFutures, or build calldata for self-submission.
api: openapi/synfutures-rwa-trading-openapi.yml
operations: [getDelegationStatus, prepareDelegation, enableDelegation, buildOrderCalldata, sendOrder, getOrder, sendCancelOrder]
generated: '2026-07-21'
method: generated
---

# Place a SynFutures RWA order via One Click

Place a tokenized-stock (RWA) order on SynFutures. Requires a `WRITABLE` key
(allows GET/POST/DELETE). These operations move funds on-chain — require
human confirmation before any `send`.

## Auth
Sign every request with the six headers (`x-api-key`, `x-api-ts`, `x-api-nonce`,
`x-api-sign` = HMAC-SHA256, `x-api-chain-id`, `x-api-p: Synfutures`). See
`authentication/synfutures-authentication.yml`.

## Steps
1. `getDelegationStatus` — check whether One Click (1CT) is already enabled for the user.
2. If not enabled: `prepareDelegation` returns an EIP-712 payload; have the user's
   wallet sign it, then `enableDelegation` with the signature.
3. Place the order one of two ways:
   - **One Click:** `sendOrder` — the backend submits the order using the delegation.
   - **Self-submit:** `buildOrderCalldata` returns calldata; submit it from your own
     wallet, then `recordOrderTx` with the resulting `txHash`.
4. `getOrder` with `{orderId}` — confirm status.
5. To cancel: `sendCancelOrder` (One Click) with `{orderId}`, or `buildCancelOrderCalldata`
   to self-submit the cancel.

## Notes
- There is no idempotency key; `x-api-nonce` is replay-prevention only. Do not blindly
  retry a `send` on a network error — reconcile via `getOrder`/`getOrderByTx` first.
- Errors return HTTP 400 with a numeric `code` in the envelope (see `errors/synfutures-problem-types.yml`).
