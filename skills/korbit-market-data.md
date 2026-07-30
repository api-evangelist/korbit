---
name: Read Korbit market data
description: >-
  Pull public market data from the Korbit exchange — tickers, order book depth, recent trades,
  candlesticks, tradable pairs and tick-size policy — without any API key.
api: openapi/korbit-openapi.yml
operations:
  - getTickers
  - getOrderbook
  - getTrades
  - getCandles
  - getCurrencyPairs
  - getTickSizePolicy
  - getCurrencies
  - getMarketAlerts
  - getTime
generated: '2026-07-19'
method: generated
source: https://docs.korbit.co.kr/llms-full.txt
---

# Read Korbit market data

All operations in this skill are **public** — no API key, no signature, no timestamp. This is the
safe surface to explore first.

- Base URL: `https://api.korbit.co.kr`
- Rate limit: 50 req/sec per IP. Watch the `Ratelimit` response header; on HTTP 429 pause until
  `Retry-After`.
- Every response uses the envelope `{"success": true, "data": …}`.
- Prices, quantities and amounts are **strings** — parse them with a decimal type, never a float.
- Timestamps are Unix milliseconds.

## Steps

1. **Discover tradable markets.** Call `getCurrencyPairs` for the symbol list and each pair's
   `status`. Symbols are lowercase `<base>_<quote>`, e.g. `btc_krw`. Call `getCurrencies` for
   per-cryptocurrency metadata (deposit/withdrawal status, confirmation counts, networks, withdrawal
   fees and limits).

2. **Get prices.** Call `getTickers`. Pass `symbol` as a comma-separated list (`btc_krw,eth_krw`) to
   scope it, or omit it for every pair. Returns 24H open/high/low/close, `prevClose`, `priceChange`,
   `priceChangePercent`, base and quote volume, `bestBidPrice` / `bestAskPrice`, and `lastTradedAt`.

3. **Get depth.** Call `getOrderbook` with a required `symbol`. Pass the optional `level` to group
   price levels — valid levels come from `getTickSizePolicy`; omit it for ungrouped depth. Each side
   returns `price`, `qty`, and `amt` (only set when grouping is used; otherwise `price * qty`).

4. **Get recent trades.** Call `getTrades` with a `symbol` for the recent public trade history.

5. **Get candles.** Call `getCandles` for historical OHLCV klines for a symbol and interval.

6. **Get order constraints.** Call `getTickSizePolicy` before quoting a price — it returns the valid
   tick size and the orderbook grouping levels for the pair. Prices not snapped to a tick are
   rejected at order time with `PRICE_TICK_SIZE_INVALID`.

7. **Check for market alerts.** Call `getMarketAlerts` for the Market Warning System (시장경보제)
   status. It returns only pairs that currently have an active alert, so an empty list is the normal
   case.

8. **Get server time** with `getTime` if you are about to make signed calls — sign against Korbit's
   clock, not the host clock.

## Notes

- There is no pagination on these endpoints. History is bounded server-side instead.
- For a live feed rather than polling, use the public WebSocket channels (`ticker`, `orderbook`,
  `trade`) — see `skills/korbit-stream-account-state.md` and
  `asyncapi/korbit-websocket-asyncapi.yml`. Public WebSocket messages can be dropped under load, so
  reconcile against a REST snapshot when correctness matters.

## Related

- Conventions: `conventions/korbit-conventions.yml`
- Rate limits: `rate-limits/korbit-rate-limits.yml`
