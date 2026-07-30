---
name: Monitor Korbit orders, fills and balances
description: >-
  Track what actually happened to orders on Korbit — reconcile a lost placement response, read open
  and recent orders, confirm executed quantity from fills, and check balances and fees.
api: openapi/korbit-openapi.yml
operations:
  - getOrders
  - getOpenOrders
  - getAllOrders
  - getMyTrades
  - getBalance
  - getTradingFeePolicy
  - getCurrentKeyInfo
generated: '2026-07-19'
method: generated
source: https://docs.korbit.co.kr/llms-full.txt
---

# Monitor Korbit orders, fills and balances

Read-only. Requires an API key with `readOrders` (orders, trades, fee policy) and `readBalances`
(balances). Every call is signed — `X-KAPI-KEY` header plus `timestamp` and `signature`.

## Steps

1. **Verify what the key can do.** Call `getCurrentKeyInfo` to read the key's `permissions`,
   `whitelist`, `allowedAccountSeqs`, `expiration` and `status`. Keys expire one year after
   creation, and permission or IP changes take up to about a minute to propagate — a call that
   should work but does not may simply be waiting on that.

2. **Reconcile a specific order.** Call `getOrders` with either `orderId` or `clientOrderId`. This
   is the operation that resolves a lost placement response: if a `createOrders` call timed out or
   returned `DUPLICATE_CLIENT_ORDER_ID`, query the `clientOrderId` here rather than resending.
   - Caveat: orders in status `expired` or `canceled` **cannot** be found by `clientOrderId`. If the
     lookup comes back empty, that is a meaningful result — the order either never landed or already
     closed unfilled.

3. **List working orders.** Call `getOpenOrders` for a single trading pair. It returns only orders in
   status `open` or `partiallyFilled`.

4. **List recent orders.** Call `getAllOrders` for a single trading pair. **Only orders created
   within the last 36 hours are queryable** — anything older must come from your own records. There
   is no pagination and no way to widen the window.

5. **Confirm what actually executed.** Call `getMyTrades` for the fills on a pair (also limited to
   the past 36 hours). Never infer execution from order status alone:
   - `filled` means the order *closed*, not that the full requested quantity traded. An `ioc` order,
     or a price-protected (`pp`) order trimmed by the protection range, closes as `filled` even when
     less than the requested quantity executed.
   - Read `filledQty` and `filledAmt` on the order, and cross-check against the fills, for the real
     executed amount and average price (`avgPrice`).
   - `partiallyFilledCanceled` means part traded and the remainder was canceled.
   - `pending` is not terminal — it can still flip to `expired` on insufficient balance or a
     `timeInForce` condition.

6. **Check balances.** Call `getBalance` for per-currency holdings. Remember that resting orders
   hold balance, so available funds are lower than total while orders are working.

7. **Check fees when sizing.** Call `getTradingFeePolicy` for the account's `makerFeeRate`,
   `takerFeeRate`, `maxFeeRate` and the fee currency per side. Leave fee headroom when sizing an
   order to the edge of a balance, or the order is rejected with `NO_BALANCE`.

## Notes

- Scope any of these to a sub-account with `accountSeq` (defaults to 1, the main account).
- Rate limit for these endpoints is 50 req/sec per account. Polling tightly across many symbols will
  hit it — prefer the private WebSocket channels (`myOrder`, `myTrade`, `myAsset`) for real-time
  state and use REST to reconcile. See `skills/korbit-stream-account-state.md`.
- Because history is capped at 36 hours, keep your own durable record of placements keyed by
  `clientOrderId`. That store is also what makes retries idempotent.

## Related

- Placement and idempotency: `skills/korbit-place-order-safely.md`
- Error codes and the full order-status table: `errors/korbit-error-codes.yml`
- Conventions: `conventions/korbit-conventions.yml`
