# Korbit

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Korbit is South Korea's first cryptocurrency exchange, founded in 2013 and headquartered in Seoul, offering KRW-denominated spot trading of bitcoin, ether and other digital assets to Korean retail and institutional customers. Korbit publishes the Korbit Open API v2, a REST and WebSocket interface covering market data (tickers, orderbook, trades, candlesticks, tick-size policy), order placement and cancellation, order and trade history, balances, cryptocurrency deposit-address management and withdrawals, and KRW deposit/withdrawal push notifications. Authentication is by API key (X-KAPI-KEY) with per-request HMAC-SHA256 or ED25519 signatures, granular key permissions and optional IP allowlisting. Korbit is unusually agent-forward for an exchange: it ships an official Go CLI with a bundled MCP server and Agent Skill, a documented llms.txt, and a downloadable local sandbox that mocks the full REST and WebSocket surface for no-risk development.

**Areas:** Company · Cryptocurrency · Cryptocurrency Exchange · Digital Assets · Trading · Market Data · Financial Services · WebSocket · South Korea · Blockchain · Real-Time

## Korbit on APIs.io

Everything profiled for Korbit is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/korbit/](https://apis.io/providers/korbit/)** — or go straight to one of the APIs or properties below.

### APIs (8)

- [Korbit Open API v2 WebSocket](https://apis.io/apis/korbit/korbit-open-api-v2-websocket/) — WebSocket streaming API for the Korbit exchange. Public channels stream ticker, orderbook and trade market data; private channels stream the authenticated…
- [Korbit Asset API](https://apis.io/apis/korbit/korbit-asset-api/) — The Asset API from Korbit — 1 operation(s) for asset.
- [Korbit Deposit (Crypto) API](https://apis.io/apis/korbit/korbit-deposit-crypto-api/) — The Deposit (Crypto) API from Korbit — 3 operation(s) for deposit (crypto).
- [Korbit Deposit/Withdrawal (KRW) API](https://apis.io/apis/korbit/korbit-deposit-withdrawal-krw-api/) — The Deposit/Withdrawal (KRW) API from Korbit — 4 operation(s) for deposit/withdrawal (krw).
- [Korbit Other API](https://apis.io/apis/korbit/korbit-other-api/) — The Other API from Korbit — 8 operation(s) for other.
- [Korbit Quotation API](https://apis.io/apis/korbit/korbit-quotation-api/) — The Quotation API from Korbit — 6 operation(s) for quotation.
- [Korbit Trading API](https://apis.io/apis/korbit/korbit-trading-api/) — The Trading API from Korbit — 4 operation(s) for trading.
- [Korbit Withdrawal (Crypto) API](https://apis.io/apis/korbit/korbit-withdrawal-crypto-api/) — The Withdrawal (Crypto) API from Korbit — 3 operation(s) for withdrawal (crypto).

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### AsyncAPI (1)

Event-driven and webhook contracts.

- [Korbit Open API v2 WebSocket](https://apis.io/asyncapis/korbit/korbit-websocket-asyncapi/)

#### Event channels (6)

Individual events and webhooks the provider emits.

- [myAsset](https://apis.io/channels/korbit/myasset/)
- [myOrder](https://apis.io/channels/korbit/myorder/)
- [myTrade](https://apis.io/channels/korbit/mytrade/)
- [orderbook](https://apis.io/channels/korbit/orderbook/)
- [ticker](https://apis.io/channels/korbit/ticker/)
- [trade](https://apis.io/channels/korbit/trade/)

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [korbit](https://apis.io/servers/korbit/korbit/)

#### Agentic access (1)

Which operations an agent can call, which act, and which need a human in the loop.

- [Korbit Agentic Access](https://apis.io/agentic-access/korbit/korbit-agentic-access/)

#### Collections (7)

Postman and Open Collection request collections.

- [Korbit Open API v2 Asset API](https://apis.io/collections/korbit/open-korbit-asset-api/)
- [Korbit Open API v2 Asset Deposit (Crypto) API](https://apis.io/collections/korbit/open-korbit-deposit-crypto-api/)
- [Korbit Open API v2 Asset Deposit/Withdrawal (KRW) API](https://apis.io/collections/korbit/open-korbit-deposit-withdrawal-krw-api/)
- [Korbit Open API v2 Asset Other API](https://apis.io/collections/korbit/open-korbit-other-api/)
- [Korbit Open API v2 Asset Quotation API](https://apis.io/collections/korbit/open-korbit-quotation-api/)
- [Korbit Open API v2 Asset Trading API](https://apis.io/collections/korbit/open-korbit-trading-api/)
- [Korbit Open API v2 Asset Withdrawal (Crypto) API](https://apis.io/collections/korbit/open-korbit-withdrawal-crypto-api/)

#### Overlays (1)

OpenAPI overlays, including conversational phrasing for each operation.

- [Korbit Openapi Overlay](https://apis.io/overlays/korbit/korbit-openapi-overlay/)

#### Security (2)

Authentication, trust centre, and vulnerability disclosure.

- [Korbit Authentication](https://apis.io/security/korbit/korbit-authentication/)
- [Korbit Domain Security](https://apis.io/security/korbit/korbit-domain-security/)

#### Rate limits (1)

Published request limits.

- [Korbit Rate Limits](https://apis.io/rate-limits/korbit/korbit-rate-limits/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://www.korbit.co.kr/)
- [Documentation](https://docs.korbit.co.kr/)
- [Getting started](https://docs.korbit.co.kr/llms/en/introduction.md)
- [API reference](https://docs.korbit.co.kr/)
- [Sign up](https://developers.korbit.co.kr)
- [Blog](https://blog.naver.com/korbit_official)
- [GitHub](https://github.com/korbit-official)

## API Evangelist rating

**Kin Score 36.8** — thin · rubric 0.24.0 · scored 2026-10-17

| Facet | Score |
|---|---|
| Discoverability | 71.7 |
| Contract quality | 60.6 |
| Contract governance | 4.5 |
| Operational transparency | 42.1 |
| Developer ergonomics | 49.4 |
| Access clarity | 0.0 |

**Agent readiness 43.6** — agent-native

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | derived (partial credit) |
| Reversibility Documented | verified |
| MCP Server | documented (partial credit) |
| Auth Clarity | bearer (partial credit) |
| Idempotency | documented (partial credit) |
| Error Semantics | verified |
| OpenAPI Examples | verified |
| Rate Limit Signal | documented (partial credit) |
| Event Surface Described | derived (partial credit) |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | — |
| Protected Resource Metadata | — |
| Dynamic Client Registration | — |
| Agentic Commerce | — |

**Regulatory alignment 15.7** — Securities & Market Data

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/korbit/](https://apis.io/providers/korbit/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Korbit: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/korbit/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
