<h1 style="text-align:center"><img src="../../assets/branding/icon-chat.svg" alt="" width="40" height="40" style="vertical-align:middle;margin-right:10px">Discord</h1>

<a href="../README.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Applications</a> · <a href="../../README.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-back.svg" width="14" height="14" alt=""> Fieldnotes</a>

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">


**Discord** is a voice, video, and text platform. This profile combines **read-only local Windows observations** of the Stable desktop client with official developer API documentation.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Overview</h2>

| Field | Value | Confidence |
| --- | --- | --- |
| Application in scope | Discord Stable desktop (primary) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local ARP |
| Publisher | Discord Inc. | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local ARP + signatures |
| Installed Stable version | `1.0.9261` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> ARP + `Discord.exe` FileVersion |
| Install root | `%LOCALAPPDATA%\Discord` (Squirrel layout) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local |
| Processes at snapshot | 6× `Discord.exe` (1 parent + 5 children) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local CIM |
| Startup | `HKCU\…\Run` → `Update.exe --processStart Discord.exe` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local |
| Official developer APIs | REST, Gateway, OAuth2, RPC | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official docs |
| Malware determination | None made | Explicitly **not** claimed |

> **🕵️ Investigation status**
>
> Hands-on case study (2026-10-10 UTC). Local Storage / tokens / cookies were **not** read. Session credentials were **not** used. This is **not** a completed security audit.
>
> Out of primary scope, ARP also surfaced an install entry for **Discord Canary** `1.0.1099` (Discord Inc.). That is a discovery note only — not a claim that Canary was running during the snapshot.

<h3 style="text-align:center">On this page</h3>

- [Purpose and scope](#purpose-and-scope)
- [Table of contents](#table-of-contents)
- [Brief assessment](#brief-assessment)
- [Safety boundaries](#safety-boundaries)
- [Sources](#sources)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Purpose and scope</h2>

**In scope:** Stable install layout, multi-process tree, updater, signatures, privacy-relevant folders, official APIs vs client internals.

**Out of scope:** Extracting tokens, cookies, or session material; authenticating as the user; probing undocumented client routes; deep analysis of non-Stable Discord channels.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Table of contents</h2>

| Document | Role |
| --- | --- |
| <a href="technical-overview.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Technical overview</a> | Install tree, modules, signatures |
| <a href="processes-and-startup.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Processes and startup</a> | Process tree, Run key, updater |
| <a href="privacy-and-telemetry.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Privacy and telemetry</a> | Controls + local Sentry/Crashpad artefacts |
| <a href="api-and-endpoints.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> API and endpoints</a> | Official / public / authenticated / internal |
| <a href="investigation-log.md" style="color:#c77dff"><img src="../../assets/branding/fieldnotes-arrow-forward.svg" width="14" height="14" alt=""> Investigation log</a> | Commands and verification |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Brief assessment</h2>

| Claim | Label |
| --- | --- |
| Stable is a Squirrel-updated per-user Electron-style desktop app at `%LOCALAPPDATA%\Discord` | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local + vendor Electron statements |
| Running Stable shows multiple `Discord.exe` processes under one parent | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local |
| Parent was launched via `Update.exe --processStart` (Squirrel) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> SquirrelSetup.log correlates updater → app launch |
| Native modules include voice, RPC, overlay, Krisp, etc. | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> `modules\` directory names |
| `Discord.exe` / `Update.exe` Authenticode Valid (Discord Inc.) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Local |
| `%APPDATA%\discord\sentry` exists (error-reporting related) | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Folder names; contents not deeply inspected |
| Official REST/Gateway/OAuth2 are the supported third-party integration APIs | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Official docs |
| Get Gateway is unauthenticated and returns a WebSocket URL | <img src="../../assets/branding/evidence-confirmed.svg" width="20" height="20" alt="Confirmed"> Docs + live check |
| Desktop client may call undocumented API routes with a user session | <img src="../../assets/branding/evidence-plausible.svg" width="20" height="20" alt="Plausible"> Plausible; not inventoried |
| Multi-process layout or Sentry folder proves malice | **Not claimed** |

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Safety boundaries</h2>

> **⚠️ Caution**
>
> Do **not** open Local Storage / Session Storage / IndexedDB for tokens. Do **not** send test events to Sentry. Do **not** automate user accounts.

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Sources</h2>

- Local observations — [investigation log](investigation-log.md)
- [Discord Developer Docs](https://discord.com/developers/docs/intro)
- [discord-api-docs](https://github.com/discord/discord-api-docs)
- [64-bit / Electron engineering post](https://discord.com/blog/how-discord-seamlessly-upgraded-millions-of-users-to-64-bit-architecture)
- [Privacy Policy](https://discord.com/privacy) · [Data Privacy Controls](https://support.discord.com/hc/en-us/articles/360004109911-Data-Privacy-Controls)

<img src="../../assets/branding/sakura-rule.svg" alt="" width="100%" style="display:block;margin:0 auto;max-width:720px">

<h2 style="text-align:center">Related resources</h2>

- [Technical overview](technical-overview.md)
- [Processes and startup](processes-and-startup.md)
- [Privacy and telemetry](privacy-and-telemetry.md)
- [API and endpoints](api-and-endpoints.md)
- [Investigation log](investigation-log.md)
