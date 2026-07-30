---
name: Fund a Korbit account and withdraw
description: >-
  Move value in and out of a Korbit account — issue cryptocurrency deposit addresses, track deposit
  confirmation, and request or cancel withdrawals against pre-registered addresses.
api: openapi/korbit-openapi.yml
operations:
  - getCoinDepositAddresses
  - getCoinDepositAddress
  - createCoinDepositAddress
  - getCoinRecentDeposits
  - getCoinDeposit
  - getCoinWithdrawableAddresses
  - getCoinWithdrawableAmount
  - createCoinWithdrawal
  - deleteCoinWithdrawal
  - getCoinRecentWithdrawals
  - getCoinWithdrawal
  - createKrwSendKrwDepositPush
  - createKrwSendKrwWithdrawalPush
  - getKrwRecentDeposits
  - getKrwRecentWithdrawals
  - getCurrencies
generated: '2026-07-19'
method: generated
source: https://docs.korbit.co.kr/llms-full.txt
---

# Fund a Korbit account and withdraw

**This flow moves real value and is irreversible once a withdrawal is broadcast.** Require explicit
human confirmation of currency, amount and destination address before calling `createCoinWithdrawal`.
Never run it autonomously on production.

Required key permissions: `readDeposits` / `writeDeposits` for deposits, `readWithdrawals` /
`writeWithdrawals` for withdrawals. Deposit and withdrawal endpoints share a tight rate limit of
**5 req/sec per account**.

## Cryptocurrency deposits

1. **Check the currency is depositable.** Call `getCurrencies` and read `depositStatus`,
   `confirmationCount` and `defaultNetwork` / `networkList` for the asset.

2. **Get an address.** Call `getCoinDepositAddress` for a single currency, or
   `getCoinDepositAddresses` for the full list. If none exists, call `createCoinDepositAddress` —
   if an address already exists, the existing one is returned, so this is safe to call again.

3. **Send funds to that address on the correct network.** Sending on the wrong network loses the
   funds; Korbit cannot reverse it.

4. **Track the deposit.** Call `getCoinRecentDeposits` for recent history and `getCoinDeposit` for
   the status of a specific deposit. Wait for the asset's `confirmationCount` before treating funds
   as usable.

## Cryptocurrency withdrawals

1. **Pre-register the destination address.** Withdrawals only go to addresses registered for API use
   in the Korbit interface. Call `getCoinWithdrawableAddresses` to read the registered list. An
   unregistered destination is rejected with `UNREGISTERED_WITHDRAWAL_ADDRESS` — resolve that in the
   UI, never by retrying.

2. **Check the withdrawable amount.** Call `getCoinWithdrawableAmount` for the currency. Also read
   `withdrawalTxFee`, `withdrawalMinAmount` and `withdrawalMaxAmountPerRequest` from
   `getCurrencies`, and confirm `withdrawalStatus` is open.

3. **Confirm with the user.** Echo currency, amount, network, fee and destination address back and
   get explicit approval.

4. **Request the withdrawal** with `createCoinWithdrawal`. Handle these terminal rejections without
   retrying:
   - `UNREGISTERED_WITHDRAWAL_ADDRESS` — address not registered for API withdrawals.
   - `FORBIDDEN_WITHDRAWAL_ADDRESS` — blocked by policy.
   - `DAILY_LIMIT_EXCEEDED` — daily withdrawal limit hit.
   - `WITHDRAWAL_SUSPENDED` — withdrawals suspended for the asset.
   - `WITHDRAWAL_ALREADY_IN_PROGRESS` — wait for the current transaction to complete.
   - `NO_BALANCE`, `INVALID_CURRENCY`, `INVALID_USER_STATUS` — resolve the underlying condition.

   **Never auto-retry this call.** There is no idempotency key on withdrawals — `clientOrderId`
   applies to order placement only. A blind retry risks a duplicate withdrawal. If the response is
   lost, reconcile with `getCoinRecentWithdrawals` before doing anything else.

5. **Track or cancel.** Call `getCoinWithdrawal` for a specific withdrawal's status and
   `getCoinRecentWithdrawals` for recent history. To cancel, call `deleteCoinWithdrawal` — it only
   works while the withdrawal is still cancellable:
   - `CANNOT_CANCEL_WITHDRAWAL` — already being processed.
   - `WITHDRAWAL_ALREADY_FINISHED` — too late.
   - `NOT_FOUND` — no such withdrawal.

## KRW deposits and withdrawals

KRW movement is **not** completed through the API. The API only sends a push notification to the
user's Korbit mobile app, where the human approves it.

1. Call `createKrwSendKrwDepositPush` or `createKrwSendKrwWithdrawalPush` to send the request to the
   mobile app.
2. The user approves in the app — there is no API call that completes this on their behalf.
3. Read history with `getKrwRecentDeposits` and `getKrwRecentWithdrawals`.

## Safety rules

- Treat every operation in this skill as `consequence: physical` — see
  `agentic-access/korbit-agentic-access.yml`.
- Confirm irreversible actions with a human before executing.
- Never retry a withdrawal on an ambiguous response; reconcile first.
- Never log the API secret, private key or signature.
- Develop against the local sandbox (`sandbox/korbit-sandbox.yml`) before touching production.

## Related

- Error codes: `errors/korbit-error-codes.yml`
- Conventions: `conventions/korbit-conventions.yml`
- Rate limits: `rate-limits/korbit-rate-limits.yml`
