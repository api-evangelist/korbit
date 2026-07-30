---
name: Place a Korbit order safely
description: >-
  Place an order on the Korbit exchange without risking a duplicate or a rejected price — mint and
  persist a clientOrderId, sync the signing clock, snap the price to the tick size, check balance
  headroom, place, then confirm the resulting status.
api: openapi/korbit-openapi.yml
operations:
  - getTime
  - getCurrencyPairs
  - getTickSizePolicy
  - getBalance
  - getTradingFeePolicy
  - createOrders
  - getOrders
  - deleteOrders
generated: '2026-07-19'
method: generated
source: https://docs.korbit.co.kr/llms-full.txt
---

# Place a Korbit order safely

This is a money-moving flow. Never run it against production until the user has explicitly enabled
live trading. Develop against the local sandbox first (`sandbox/korbit-sandbox.yml`) by pointing the
base URL at `http://127.0.0.1:9999`.

If the environment can run a binary or speak MCP, prefer the official CLI (`cli/korbit-cli.yml`) —
it already implements every rule below. Use these steps when building a client directly.

## Before you start

- Base URL: `https://api.korbit.co.kr`
- Auth: `X-KAPI-KEY` header, plus `timestamp` and `signature` parameters on every call.
- Required key permissions: `writeOrders` to place/cancel, `readOrders` to query, `readBalances` to
  check funds.
- Rate limits: order placement and cancellation are 30 req/sec per account. See
  `rate-limits/korbit-rate-limits.yml`.

## Steps

1. **Sync the signing clock.** Call `getTime` and sign against Korbit's server clock, not the host
   clock. The accepted window is asymmetric: `recvWindow` (default 5000 ms, max 60000 ms) only
   widens the past side, while the future bound is a fixed +1000 ms. A clock even slightly ahead
   fails every signed request and raising `recvWindow` cannot help. A drifted clock surfaces as
   `EXCEED_TIME_WINDOW`.

2. **Confirm the market is tradable.** Call `getCurrencyPairs` and check the pair's `status`. Call
   `getMarketAlerts` if you want to avoid pairs under a Market Warning System alert.

3. **Snap the price to the tick size.** Call `getTickSizePolicy` for the symbol and round the
   intended price onto a valid tick. An unsnapped price is rejected with
   `PRICE_TICK_SIZE_INVALID`.

4. **Check the order is within bounds.** `qty * price` must be at least 5,000 KRW and no more than
   1 billion KRW, or the order is rejected with `ORDER_VALUE_TOO_SMALL` / `ORDER_VALUE_TOO_LARGE`.

5. **Check balance headroom.** Call `getBalance` (and `getTradingFeePolicy` if sizing to the edge)
   and leave room for fees. Insufficient funds surface as `NO_BALANCE`, and a `pending` order can
   later flip to `expired` if the balance is not there when it is processed.

6. **Mint and persist a `clientOrderId` BEFORE sending.** This is the idempotency key and the single
   most important step.
   - Mint once per placement decision — a UUIDv7 is the recommended default (time-ordered, unique,
     exactly 36 chars, fits the `[0-9a-zA-Z.:_-]{1,36}` charset without truncation).
   - Persist it in your own durable store, keyed by your internal placement id, *before* the request
     goes out. On any retry or restart, reload and reuse it — never regenerate.
   - Never derive it from the order's business identity (a hash of symbol/side/price/qty). A
     `clientOrderId` effectively never frees up, so a genuinely-distinct later order repeating those
     fields would collide and be rejected.
   - Never truncate an id to fit — truncation can collapse two distinct placements onto one value.

7. **Place the order** with `createOrders` (`POST /v2/orders`, body as
   `application/x-www-form-urlencoded`). Required: `symbol`, `side`, `orderType`. Then:
   - limit order → send `price` and `qty`; `timeInForce` defaults to `gtc`.
   - market/BBO sell → send `qty`, omit `price`.
   - market/BBO buy → send `amt` (the purchase amount), omit `price` and `qty`.
   - `orderType: best` → `timeInForce` and `bestNth` (1–5) are both required.
   - Always include your persisted `clientOrderId`.
   - Scope to a sub-account with `accountSeq` when the account is not the default (1).

8. **Handle the response as follows.**
   - Success returns `{"success": true, "data": {"orderId": …}}`.
   - `DUPLICATE_CLIENT_ORDER_ID` means a prior attempt already registered *this* order. **Treat it
     as success.** Look it up with `getOrders` using the `clientOrderId` — do not resend.
   - A network timeout does **not** tell you whether the order landed. Never blindly resend. Query
     `getOrders` by `clientOrderId` to find out, then act on what you find.
   - Retry only transient failures with backoff: no HTTP status (network), 429, 5xx, or `TRY_AGAIN`.
     On 429 honor `Retry-After` or the `Ratelimit` reset window.

9. **Confirm the terminal status** with `getOrders`. Do not assume a fill.
   - `filled` does **not** mean the full requested quantity executed — an `ioc` order, or a
     price-protected (`pp`) order trimmed by the protection range, also closes as `filled`. Always
     read `filledQty` / `filledAmt` for what actually executed.
   - `pending` may still flip to `expired` on insufficient balance or a `timeInForce` condition.
   - Other statuses: `open`, `canceled`, `partiallyFilled`, `partiallyFilledCanceled`.

10. **To cancel**, call `deleteOrders` with the `orderId` or `clientOrderId`. `TRY_AGAIN` means the
    order is mid-processing — retry shortly. `ORDER_ALREADY_FILLED`, `ORDER_ALREADY_CANCELED`,
    `ORDER_ALREADY_EXPIRED` and `ORDER_NOT_FOUND` are all terminal: re-query rather than retrying.

## Safety rules

- Confirm the intended symbol, side, quantity and price back to the user before placing anything on
  production.
- Never auto-retry a money-moving write without the idempotency reconciliation in step 8.
- Never log or echo the API secret, private key or request signature. Log `clientOrderId`,
  `orderId`, status transitions and error codes instead.
- Orders with status `expired` or `canceled` cannot be looked up by `clientOrderId`, and the id
  becomes reusable roughly three days after the order closes.

## Related

- Conventions and the full idempotency contract: `conventions/korbit-conventions.yml`
- Error codes and order statuses: `errors/korbit-error-codes.yml`
- Rate limits: `rate-limits/korbit-rate-limits.yml`
