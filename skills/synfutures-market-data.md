---
name: Read SynFutures RWA market data
description: Fetch tradable symbols, real-time quotes, K-line history, and underlying prices from the SynFutures RWA Trading API.
api: openapi/synfutures-rwa-trading-openapi.yml
operations: [listSymbols, getPrice, getPriceHistory, getUnderlyingPrice, getMarketConfig]
generated: '2026-07-21'
method: generated
---

# Read SynFutures RWA market data

Read-only market data from the SynFutures RWA (tokenized stock) Trading API.

## Auth
Every `/api/v1` request needs six headers: `x-api-key`, `x-api-ts` (unix ms, ≤45s drift),
`x-api-nonce` (unique per key within the window), `x-api-sign` (HMAC-SHA256 hex),
`x-api-chain-id` (e.g. `8453` Base), and `x-api-p: Synfutures`. A `READ_ONLY` key is
sufficient for these GET operations. The key must have an IP whitelist configured — an
empty whitelist rejects every request with `401`.

## Steps
1. `getMarketConfig` — read on-chain market config.
2. `listSymbols` — get the tradable symbols and their tradability status.
3. `getPrice` with `{symbol}` — real-time quote.
4. `getPriceHistory` with `{symbol}` — K-line history.
5. `getUnderlyingPrice` with `{symbol}` — underlying reference quote.

## Conventions
- Responses use the envelope `{code, errMsg, data, uuid, t}`; success is `code: 200`.
- List endpoints paginate with `page` (default 1) and `limit` (default 10, max 100),
  returning `total` and `rows`.
- On `429`, back off — the limit is 1,200 requests/min/IP.
