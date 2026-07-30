---
name: Stream Korbit market and account state over WebSocket
description: >-
  Subscribe to Korbit's public market-data channels and private account channels, handle the
  subscribe/control protocol, and reconcile against REST after any reconnect.
api: asyncapi/korbit-websocket-asyncapi.yml
operations:
  - receiveTicker
  - receiveOrderbook
  - receiveTrade
  - receiveMyOrder
  - receiveMyTrade
  - receiveMyAsset
rest_operations:
  - getOrderbook
  - getOrders
  - getOpenOrders
  - getBalance
  - getTrades
generated: '2026-07-19'
method: generated
source: https://docs.korbit.co.kr/llms-full.txt
---

# Stream Korbit market and account state over WebSocket

Use the WebSocket API instead of polling REST for live state. It is the recommended way to track
orders, fills and balances in real time.

- Public: `wss://ws-api.korbit.co.kr/v2/public` — no authentication.
- Private: `wss://ws-api.korbit.co.kr/v2/private` — signed exactly like REST: `timestamp` and
  `signature` go in the **URL query string**, `X-KAPI-KEY` goes in the **connection header**.
- Sandbox: `ws://127.0.0.1:9999/v2/public` and `/v2/private`.

## Channels

| Channel | Auth | Streams |
|---|---|---|
| `ticker` | public | Latest pricing for a symbol. |
| `orderbook` | public | Depth for a symbol, up to 30 price levels per side. |
| `trade` | public | Real-time trades. |
| `myOrder` | `readOrders` | Changes to your orders. |
| `myTrade` | `readOrders` | Fills on your orders. |
| `myAsset` | `readBalances` | Changes to your balances. |

## Steps

1. **Connect** to the public or private endpoint. For private, build the signature over the query
   string the same way as a REST call and set the `X-KAPI-KEY` header on the connection.

2. **Subscribe** by sending an array of subscription objects:

   ```json
   [{"requestId": 1, "method": "subscribe", "type": "ticker", "symbols": ["btc_krw"]}]
   ```

   Use `"method": "unsubscribe"` to stop. Set a distinct `requestId` per request so you can match
   the acknowledgement.

3. **Distinguish control messages from data.** Control messages carry a `status` field; data
   messages do not.

   ```json
   {"requestId": 1, "status": "success"}
   {"requestId": 1, "status": "fail", "code": "INVALID_SYMBOL", "message": "..."}
   {"status": "error", "message": "..."}
   ```

   Treat any `status: "fail"` or `status: "error"` as a subscription that is not live — do not
   assume you are receiving data just because the socket is open.

4. **Handle public message loss.** Public messages **can be dropped under load**. If correctness
   matters, periodically reconcile against a REST snapshot (`getOrderbook`, `getTrades`).

5. **Handle reconnects.** On reconnect, refetch a fresh REST snapshot before trusting incremental
   updates again — you may have missed messages while disconnected.
   - For the book: `getOrderbook`.
   - For account state: `getOpenOrders` / `getOrders` and `getBalance`.

6. **Trust private delivery, but not the connection.** Private messages are **not** dropped, but the
   socket can be force-closed. On reconnect, reconcile orders and balances via REST as above.

7. **Read the snapshot semantics.** The `trade` subscription snapshot carries only the latest
   trade(s), not full history — use `getTrades` for recent history.

## Notes

- Streaming private channels is the way to avoid burning the 50 req/sec per-account REST budget on
  polling.
- The scripting hooks in `korbit-cli` for building a streaming bot are documented as **experimental**
  and may change; do not build long-lived bots on them yet.
- `korbit-cli` maintains real-time account state from these channels with no REST polling, including
  local in-flight balance holds — useful as a reference implementation.

## Related

- AsyncAPI definition of every channel and payload: `asyncapi/korbit-websocket-asyncapi.yml`
- Reconciliation via REST: `skills/korbit-monitor-orders-and-fills.md`
- Conventions: `conventions/korbit-conventions.yml`
