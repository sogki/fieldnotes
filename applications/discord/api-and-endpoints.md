<h1 style="text-align:center">Discord — API and endpoints</h1>

[<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Case overview](README.md) · [<img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes](../../README.md)

Categorised notes on Discord’s network interfaces. Prefer [official developer documentation](https://discord.com/developers/docs/intro) and [discord-api-docs](https://github.com/discord/discord-api-docs). This page is **not** a full API reference—only representative endpoints that explain architecture.

> **ℹ️ Note**
>
> **Authentication** proves who the caller is (bot token, OAuth2 bearer token, etc.). **Authorisation** decides what that identity may do (OAuth2 scopes, guild permissions, privileged Gateway intents). A valid token without the right scope or permission still fails.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

- [A. Official public APIs](#a-official-public-apis)
- [B. Publicly accessible endpoints](#b-publicly-accessible-endpoints)
- [C. Authenticated APIs](#c-authenticated-apis)
- [D. Internal or undocumented application endpoints](#d-internal-or-undocumented-application-endpoints)
- [E. Endpoint evidence register](#e-endpoint-evidence-register)
- [Distinguishing developer APIs from desktop-client traffic](#distinguishing-developer-apis-from-desktop-client-traffic)
- [Sources](#sources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">A. Official public APIs</h2>

<h3 style="text-align:center">REST HTTP API</h3>

| Item | Detail | Confidence |
| --- | --- | --- |
| Base URL | `https://discord.com/api` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official reference |
| Versioning | Path prefix `/v{n}` (e.g. `/v10`); omitting version uses Discord’s current default | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official reference |
| Recommended version in current docs | API v10 (available) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official reference |
| Auth header forms | `Authorization: Bot <token>`, `Authorization: Bearer <token>`, or Basic for some OAuth2 token endpoints | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official reference |
| Transport | TLS 1.2+ for HTTP-layer Discord API services | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official reference |

Representative resource-style paths (all require appropriate auth unless noted in section B):

| Method | Path | Purpose | Auth / scope notes |
| --- | --- | --- | --- |
| `GET` | `/users/@me` | Current user | Bot token, or OAuth2 `identify` (`email` for email fields) |
| `GET` | `/users/{user.id}` | User by ID | Authenticated caller; returns a user object |
| `GET` | `/users/@me/guilds` | Current user’s guilds | OAuth2 `guilds` for OAuth2 apps |
| `GET` | `/channels/{channel.id}` | Channel object | Bot/user token with access |
| `GET` | `/guilds/{guild.id}` | Guild object | Authenticated; permissions apply |
| `POST` | `/channels/{channel.id}/messages` | Send message | Bot/user with `SEND_MESSAGES` (and related) permissions |

Full catalogues live in the official docs (Channels, Guilds, Messages, Users, Applications, Interactions, etc.).

<h3 style="text-align:center">Gateway WebSocket API</h3>

| Item | Detail | Confidence |
| --- | --- | --- |
| Role | Persistent WebSocket for real-time events (and some client updates) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official Gateway docs |
| URL discovery | `GET /gateway` or `GET /gateway/bot`, then connect to returned `url` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official |
| Example URL form | `wss://gateway.discord.gg/?v=10&encoding=json` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official |
| Identify | Opcode 2 Identify after Hello; bots use bot token; intents select event groups | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official |
| Payload size | Outbound events must not exceed 4096 bytes | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official |

<h3 style="text-align:center">OAuth2</h3>

| URL | Role |
| --- | --- |
| `https://discord.com/oauth2/authorize` | Authorization endpoint |
| `https://discord.com/api/oauth2/token` | Token exchange / refresh |
| `https://discord.com/api/oauth2/token/revoke` | Token revocation |

Common scopes (non-exhaustive): `identify`, `email`, `guilds`, `guilds.join`, `guilds.members.read`, `connections`, `bot`, `applications.commands`, `webhook.incoming`, plus partner-gated `rpc*` / `relationships.read` / others. Some scopes require Discord approval.

Token endpoint content type: `application/x-www-form-urlencoded` only (JSON rejected).

<h3 style="text-align:center">Rate limits (official summary)</h3>

| Topic | Documented behaviour | Confidence |
| --- | --- | --- |
| Per-route limits | Returned via `X-RateLimit-*` headers; do not hard-code | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Global bot limit | Up to 50 requests/second per bot (IP-based if unauthenticated) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| HTTP 429 | Includes `retry_after`; respect `Retry-After` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |
| Invalid request / Cloudflare bans | Too many 401/403/429-style invalids can temporarily restrict an IP (docs cite 10,000 / 10 minutes) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> |

<h3 style="text-align:center">Documented local RPC</h3>

Discord documents a **local RPC** server on the running client for IPC / Rich Presence style integration. Some RPC capabilities and OAuth scopes are partner-gated. This is an official surface, but it is **local**, not a public internet API.

Local Stable install includes a `discord_rpc` module directory under `app-1.0.9261\modules\` (<img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> observed 2026-10-10), consistent with shipping RPC support. Port binding was not probed.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">B. Publicly accessible endpoints</h2>

Documented here only where evidence supports **unauthenticated** access. Public accessibility ≠ permission to scrape, abuse, or ignore Terms of Service / rate limits.

<h3 style="text-align:center">Get Gateway</h3>

| Field | Value |
| --- | --- |
| Endpoint | `GET https://discord.com/api/v10/gateway` |
| Auth | None documented for this route |
| Returns | JSON with a `url` field (WebSocket base), e.g. `wss://gateway.discord.gg` |
| Evidence | Official Gateway docs |
| Live check | Performed 2026-10-10 — see [investigation log](investigation-log.md#live-verification) |
| Limits | Cached responses possible; still subject to IP-based global limits if abused |

Connecting to the Gateway WebSocket and completing Identify **does** require a token for useful sessions. Fetching the URL alone does not grant account access.

<h3 style="text-align:center">Get Invite (conditional)</h3>

| Field | Value |
| --- | --- |
| Endpoint | `GET /invites/{invite.code}` |
| Auth | Official docs describe returning an invite object; many invite previews work without a user login in the product UI |
| Independent test here | **Not performed** (would require choosing a real invite code) |
| Limits | Rate limits apply; do not enumerate invite codes |

<h3 style="text-align:center">CDN / image URLs</h3>

Official image formatting docs describe CDN URL patterns for avatars, icons, emojis, and other assets. Asset hashes/IDs typically come from API objects. Fetching a known public asset URL may succeed without an `Authorization` header; that does not make private attachments public.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">C. Authenticated APIs</h2>

| Credential type | Used for | Notes |
| --- | --- | --- |
| Bot token | Bots and apps acting as bot users | Prefix `Bot` in `Authorization`; never commit tokens |
| OAuth2 bearer token | Acting on behalf of a user who authorised scopes | Prefix `Bearer`; scoped |
| Client ID + secret (Basic or form body) | OAuth2 token / revoke endpoints | Confidential; server-side only |
| User account token / browser session | First-party client login | **Not** a supported bot auth method; automating user accounts violates Discord developer policy |

Almost all resource REST routes and Gateway Identify flows require one of the supported developer credentials above. Authorisation still depends on scopes, intents, and guild/channel permissions.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">D. Internal or undocumented application endpoints</h2>

| Topic | Status |
| --- | --- |
| First-party desktop/web client routes under `discord.com/api` that are absent from the public developer reference | **Undocumented/internal — support status unverified** |
| Exact analytics / experiment / billing client paths | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> **Unknown** in this investigation — not enumerated |
| Private Cloudflare or edge behaviours | Out of scope |

Fieldnotes does **not** list guessed path templates. Community reverse-engineering notes are treated as unverified unless corroborated by Discord.

Discord’s own developer docs note that the Gateway carries nuances relevant to first-party clients that may not matter to apps—another reminder that client protocol surface ⊇ bot surface.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">E. Endpoint evidence register</h2>

Only rows with evidence are populated. Secrets and personal IDs omitted.

| Service / host | Path | Method / protocol | Classification | Authentication | Purpose | Evidence | Confidence | Limitations |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `discord.com` | `/api/v10/gateway` | `GET` HTTP | Official + publicly accessible (URL only) | None documented | Return Gateway WebSocket base URL | Official Gateway docs; live HTTP 200 on 2026-10-10 (re-checked same day) returning `{"url":"wss://gateway.discord.gg"}` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Confirmed | Does not authenticate a session; abuse still rate-limited |
| `discord.com` | `/api/v10/gateway/bot` | `GET` HTTP | Official | Bot token | Gateway URL + shard / session start limit metadata | Official Gateway docs | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Confirmed (docs); not live-tested here | Requires bot token |
| `gateway.discord.gg` | `/` (query `v`, `encoding`, optional `compress`) | WebSocket | Official | Token via Identify | Real-time events | Official Gateway docs | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Confirmed (docs) | Must heartbeat; intents apply; not live-connected here |
| `discord.com` | `/api/v10/users/@me` | `GET` HTTP | Official / authenticated | Bot or OAuth2 (`identify`) | Current user object | Official User resource docs | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Confirmed (docs) | Not live-tested; never log tokens |
| `discord.com` | `/api/v10/users/@me/guilds` | `GET` HTTP | Official / authenticated | OAuth2 `guilds` (for OAuth2) | Partial guild list | Official User resource docs | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Confirmed (docs) | Not live-tested |
| `discord.com` | `/oauth2/authorize` | `GET` HTTP (browser) | Official | User interactive login/consent | OAuth2 authorization | Official OAuth2 docs | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Confirmed (docs) | Not a machine-to-machine data API by itself |
| `discord.com` | `/api/oauth2/token` | `POST` HTTP | Official / authenticated | Client credentials + auth code/refresh | Issue / refresh tokens | Official OAuth2 docs | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Confirmed (docs) | `application/x-www-form-urlencoded` only |
| `discord.com` | `/api/v10/invites/{invite.code}` | `GET` HTTP | Official; often usable without user token for valid codes | None documented for basic fetch; product behaviour may vary | Invite preview metadata | Official Invite resource docs | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Strongly supported (docs); public access not independently tested here | Do not enumerate codes |
| CDN hosts per image docs | `/…` asset paths | `GET` HTTP | Official asset delivery | Often none for public assets | Avatars, icons, emoji images | Official image formatting docs | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Strongly supported | Private attachments are not public merely because CDN hosts exist |
| Localhost (RPC) | Documented RPC ports / IPC | IPC / local WS (per RPC docs) | Official local API | App client ID + RPC OAuth flows as documented | Rich Presence / local client control | Official RPC docs | <img src="../../assets/branding/evidence-strong.svg" width="20" height="20" alt="Strongly supported"> Strongly supported (docs) | Partner scopes may be required; not probed here |
| First-party client-only API routes | *(not listed)* | HTTP | Internal/unknown | User session | Client features beyond public bot API | No inventory performed | <img src="../../assets/branding/evidence-unknown.svg" width="20" height="20" alt="Unknown"> Unknown | **Undocumented/internal — support status unverified** |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Distinguishing developer APIs from desktop-client traffic</h2>

| Signal | Developer / bot API | Desktop client session |
| --- | --- | --- |
| Auth | `Bot` / `Bearer` tokens from Developer Portal / OAuth2 | User login session (cookies / token storage inside the app) |
| Documentation | Stable public docs + github.com/discord/discord-api-docs | Partial overlap; extras undocumented |
| Intended use | Apps, bots, integrations | First-party UX |
| Policy | Developer Terms; no user-account automation | Ordinary product use |
| Observation method | Documented requests you control | Optional local network inspection of *your* client only — do not publish tokens |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Sources</h2>

- [API Reference](https://discord.com/developers/docs/reference)
- [OAuth2](https://discord.com/developers/docs/topics/oauth2)
- [Gateway](https://discord.com/developers/docs/topics/gateway)
- [Rate Limits](https://discord.com/developers/docs/topics/rate-limits)
- [User Resource](https://discord.com/developers/docs/resources/user)
- [Invite Resource](https://discord.com/developers/docs/resources/invite)
- [RPC](https://docs.discord.com/developers/topics/rpc)
- [discord-api-docs](https://github.com/discord/discord-api-docs)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Technical overview](technical-overview.md)
- [Privacy and telemetry](privacy-and-telemetry.md)
- [Investigation log](investigation-log.md)
